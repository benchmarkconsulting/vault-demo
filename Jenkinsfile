pipeline {
    agent none
    stages {
        stage ('test') {
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
                echo 'hello world'
            }
        }
    }
}