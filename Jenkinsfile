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
		     
                     kubectl  --token=$TOKEN apply -f elf.namespace.yaml -n elf
		     kubectl  --token=$TOKEN apply -f ingress.yaml -n elf
		     helm --token=$TOKEN repo  add elastic https://helm.elastic.co
		    helm --token=$TOKEN repo  add fluent https://fluent.github.io/helm-charts
		    helm --token=$TOKEN repo update
		    helm --token=$TOKEN install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
		    helm --token=$TOKEN install  fluent-bit fluent/fluent-bit --namespace=elf
		    helm --token=$TOKEN install  kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=LoadBalancer
		     kubectl --token=$TOKEN -n elf get all
		     
		      
                  '''
              }
          }
      }
  }
}

