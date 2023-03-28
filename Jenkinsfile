podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: centos
        image: centos
        command:
        - sleep
        args:
        - 99d
        restartPolicy: Never
''') 
{
  node(POD_LABEL) {
    stage('calculator') {
		git 'https://github.com/arun-uml/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
		container('centos') {			
			stage('Start calculator') {
			  sh '''
			  pwd
			  cd Chapter08/sample1
			  curl -ik -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT/api/v1/namespaces/staging/deployments -XPOST -H "Content-type: application/yaml" --data-binary @hazelcast.yaml
			  curl -ik -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT/api/v1/namespaces/staging/deployments -XPOST -H "Content-type: application/yaml" --data-binary @calculator.yaml
			  '''
			}
			stage("Test calculator") {
			  sh '''
			  test $(curl calculator-service:8080/sum?a=2\\&b=3) -eq 5 && echo 'Pass' || 'Fail'
			  test $(curl calculator-service:8080/div?a=6\\&b=3) -eq 2 && echo 'Pass' || 'Fail'
			  test "$(curl calculator-service:8080/div?a=5\\&b=0)" = "Division by 0" && echo 'Fail:Division by 0' || 'Fail'
			  '''
          }
		}
	}
  }
}

