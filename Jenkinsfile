def secrets = [
        [path: 'secret/data/dev/wordpress', engineVersion: 2, secretValues: [
            [envVar: 'devfirstdbpass', vaultKey: 'firstdbpass'],
	    [envVar: 'devmariadbpassword', vaultKey: 'dbpassword'],
            [envVar: 'devwordppassword', vaultKey: 'password']]],
        [path: 'secret/data/qa/wordpress', engineVersion: 2, secretValues: [
            [envVar: 'qafirstdbpass', vaultKey: 'firstdbpass'],
	    [envVar: 'qamariadbpassword', vaultKey: 'dbpassword'],
            [envVar: 'qawordppassword', vaultKey: 'password']]],
        [path: 'secret/integration/jira', engineVersion: 2, secretValues: [
            [envVar: 'jirauser', vaultKey: 'user'],
            [envVar: 'jirapass', vaultKey: 'password']]],
        [path: 'secret/integration/sonar', engineVersion: 2, secretValues: [
            [envVar: 'AUTH', vaultKey: 'token']]]
    ]

def configuration = [vaultUrl: 'http://vault.mc1985.net:8200', vaultCredentialId: 'vault-approle', engineVersion: 1]
                                             
pipeline {
    agent none
    stages {
        stage ('Code Quality') {
            agent {
        kubernetes {
            yaml """
    kind: Pod
    spec:
      containers:
      - name: jnlp
        image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
        imagePullPolicy: Always
        tty: false
        """}
    }
            steps {
                withVault([configuration: configuration, vaultSecrets: secrets]) {
                script {
                SONAR_PROJECT = "frontend"    
	            SONAR_URL = "http://sonar.mc1985.net"
                response = httpRequest url: "${SONAR_URL}/api/measures/search_history?component=${SONAR_PROJECT}&metrics=bugs,code_smells,vulnerabilities,security_hotspots,alert_status", 
                    customHeaders: [[name: 'Authorization', 
                    value: "Basic $AUTH"]], 
                    ignoreSslErrors: true, 
                    httpMode: 'GET'
                status = readJSON(text: response.content)
                }
            }
        }
        }
        stage ('Sonar Results') {
            agent {
        kubernetes {
            yaml """
    kind: Pod
    spec:
      containers:
      - name: jnlp
        image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
        imagePullPolicy: Always
        tty: false
        """}
    }
            steps {
                withVault([configuration: configuration, vaultSecrets: secrets]) {
            script {
                SONAR_PROJECT = "frontend"
               echo """
                      Quality Gate Results for: ${SONAR_PROJECT}
                      -----------------------------------------
                      VULNERABILITIES:   ${status.measures[0].history[-1].value}
                      SECURITY_HOTSPOTS: ${status.measures[1].history[-1].value}
                      QUALITY_STATUS:    ${status.measures[4].history[-1].value}
                      CODE_SMELLS:       ${status.measures[3].history[-1].value}
                      BUGS:              ${status.measures[2].history[-1].value}
                      DATE:              ${status.measures[0].history[-1].date}
                    """
                    }
                }
            }
        }
         stage ('Post results to JIRA') {
            agent {
        kubernetes {
            yaml """
    kind: Pod
    spec:
      containers:
      - name: jnlp
        image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
        imagePullPolicy: Always
        tty: false
        """}
    }
            steps {
                withVault([configuration: configuration, vaultSecrets: secrets]) {
                sh  """
                curl --request POST \
  --url 'https://benchmarkcorp.atlassian.net/rest/api/3/issue' \
  --user '$jirauser:$jirapass' \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{
"fields":{
  "project": {
    "key": "MD"
},
"summary": "SonarQube Results for frontend Jenkins-${BUILD_ID}",
"issuetype": {
    "id": "10010"    
},
"project": {
    "key": "MD"
},
"description": {
    "type": "doc",
    "version": 1,
    "content": [
        {
        "type": "paragraph",
        "content": [
            {
            "text": "Quality Gate Results for: ${SONAR_PROJECT}\\n-----------------------------------------\\nVULNERABILITIES:   214\\nSECURITY_HOTSPOTS: 4\\nQUALITY_STATUS:    0\\nCODE_SMELLS:       0\\nBUGS:              OK\\nDATE:              2020-12-03T23:28:46+0000",
            "type": "text"
            }
        ]
        }
    ]
    }
}
}'
"""    
                }
            }
        }
        stage ('deploy-dev') {
            agent {
        kubernetes {
            yaml """
    kind: Pod
    spec:
      containers:
      - name: jnlp
        image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
        imagePullPolicy: Always
        tty: false
        """}
    }
            steps {
                withVault([configuration: configuration, vaultSecrets: secrets]) {
                    sh "helm repo add bitnami https://charts.bitnami.com/bitnami"
                    sh "helm upgrade --install dev-wordpress bitnami/wordpress --set mariadb.auth.rootPassword=$devfirstdbpass --set wordpressPassword=$devwordppassword --set mariadb.auth.password=$devmariadbpassword -n default"
                }
            }
        }
    stage ('deploy-qa') {
            agent {
        kubernetes {
            yaml """
    kind: Pod
    spec:
      containers:
      - name: jnlp
        image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
        imagePullPolicy: Always
        tty: false
        """}
    }
            steps {
                withVault([configuration: configuration, vaultSecrets: secrets]) {
                    sh "helm repo add bitnami https://charts.bitnami.com/bitnami"
                    sh "helm upgrade --install qa-wordpress bitnami/wordpress --set mariadb.auth.rootPassword=$qafirstdbpass --set wordpressPassword=$qawordppassword --set mariadb.auth.password=$qamariadbpassword -n default"
                }
            }
        }
    }
}
