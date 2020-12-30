pipeline {
    agent none
    stages {
        stage ('Deploy Cart Redis') {
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
                echo 'Deploy Redis'
            }
        }
       stage ('Deploy Cart MS') {
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
                echo 'Deploy Cart MS'
            }
        }
    }
}