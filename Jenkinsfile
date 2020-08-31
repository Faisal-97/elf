pipeline {
      agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  - name: helm
    image: alpine/helm
    command:
    - cat
    tty: true
"""
    }
  }
  environment {
    TOKEN=credentials('59185ca6-4734-444a-9e43-423930a001fd')
  }

    stages {
      stage("Deploy") {
          steps {
              container('kubectl') {
                  sh '''
		      kubectl --token=$TOKEN create namespace elf       
		      '''
              }
          }
      }
        stage('helm-deploy') {
	  steps {
                  container('helm') {
                      sh '''
  			helm repo add elastic https://helm.elastic.co
			helm repo add fluent https://fluent.github.io/helm-charts
			helm repo update
			helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
			helm install fluent-bit fluent/fluent-bit --namespace=elf
			helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort
		    '''
                    }
            }
        }
    }
}
