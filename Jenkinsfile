pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
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
    TOKEN=credentials('a1128c0f-36a9-4149-a8d3-b483c340a8b6')
  }
  stages { 
      stage("deploy") {
          steps {
              container('kubectl') {
                  sh '''
		     
                     kubectl apply -f elf.namespace.yaml -n elf
                     helm repo add elastic https://helm.elastic.co
		     helm repo add fluent https://fluent.github.io/helm-charts
		     helm repo update
		     helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
		     helm install fluent-bit fluent/fluent-bit --namespace=elf
		     helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort
		     kubectl run random-logger --image=chentex/random-logger -n elf
		     kubectl apply -f ingress.yaml -n elf
                      
                  '''
              }
          }
      }
  }
}

