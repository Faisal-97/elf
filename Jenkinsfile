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
    TOKEN=credentials('a6b0dd41-8e48-4b83-9d7f-7eb7a1ca06fa')
  }
  stages { 
      stage("deploy") {
          steps {
              container('kubectl') {
                  sh '''
		     
                     source elf.sh
                  '''
              }
          }
      }
  }
}

