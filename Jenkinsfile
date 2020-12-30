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
                sh 'kubectl apply -f ./k8s-manifests/cart-redis.yaml -n default'
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
                sh 'kubectl apply -f ./k8s-manifests/cart-ms.yaml -n default'
                sh 'wget http://cart.default.svc.cluster.local'
            }
        }
    }
}