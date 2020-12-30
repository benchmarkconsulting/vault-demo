agent {
    kubernetes {
        label podlabel
        yaml """
kind: Pod
spec:
  containers:
  - name: jnlp
    image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
    imagePullPolicy: Always
    tty: false
"""
    }
    stages {
        stage ('test') {
            agent {
                label 'jnlp'
            }
            steps {
                echo 
            }
        }
    }
}