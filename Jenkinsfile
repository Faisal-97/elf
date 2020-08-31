pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    service_name: elf-service
    service_type: REST
spec:
  containers:
  - name: dnd
    image: docker:latest
    command: 
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock  
      type: Socket
"""
    }
  }
  environment {
    CREDS = credentials('omega_docker_creds')
    DOCKER_NAMESPACE = 'omegaproject1'
    SERVICE_NAME = 'elf-service'
    TOKEN=credentials('2407e45d-3c81-4f7a-8817-36e3bae44ff2')
  }
  stages {
      stage("Build") {
          steps {
              container('dnd') {
                  sh '''
                      cd $SERVICE_NAME
                      docker build --tag $DOCKER_NAMESPACE/$SERVICE_NAME .
		      helm repo add elastic https://helm.elastic.co
		      helm repo add fluent https://fluent.github.io/helm-charts
	       	      helm repo update
	 	      helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
		      helm install fluent-bit fluent/fluent-bit --namespace=elf
	      	      helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=LoadBalancer
                  '''
              }
          }
      }
      stage("Release") {
          steps {
              container('dnd') {
                  sh '''
                      docker login -p $CREDS_PSW -u $CREDS_USR
                      docker push $DOCKER_NAMESPACE/$SERVICE_NAME
                  '''
              }
          }
      }
      stage("Deploy") {
          steps {
              container('kubectl') {
                  sh '''
                      
                      kubectl --token=$TOKEN -n test apply -f ingress.yaml -f elf.namespace.yaml

                  '''
              }
          }
      }
  }
}

