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
		     ./elf.sh
		      sleep 90
		     kubectl --token=$TOKEN -n elf get all
                  '''
              }
          }
      }
  }
}

