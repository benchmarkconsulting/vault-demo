podTemplate(yaml: """
kind: Pod
spec:
  containers:
  - name: jnlp
    image: gcr.io/manage-261115/jenkins-slave-tools:v1.2
    imagePullPolicy: Always
    tty: false
"""
) {
    node(POD_LABEL){
        container('jnlp') {
    }

    }
}