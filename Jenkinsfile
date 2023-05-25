pipeline{
    agent any
    parameters{
      string(name: 'region', defaultValue : 'us-east-1', description: "AWS region.")
      string(name: 'cluster', defaultValue: 'prak', description: "EKS cluster name")
    }
     environment {
        KUBECONFIG = '/root/.kube/config'
    }
    stages {
  stage('setup') {
    steps {
     
        sh """
            [ ! -d bin ] && mkdir bin
            ( cd bin
            curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_\$(uname -s)_amd64.tar.gz" | tar xzf -
            # 'latest' kubectl is backward compatible with older api versions
            curl --silent -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl
            curl -fsSL -o - https://get.helm.sh/helm-v3.6.3-linux-amd64.tar.gz | tar -xzf - linux-amd64/helm
            mv linux-amd64/helm .
            rm -rf linux-amd64
            chmod u+x eksctl kubectl helm
            ls -l eksctl kubectl helm )
          """

    }
  }


  stage('deploy') {
    steps {
      sh "aws eks update-kubeconfig --name ${params.cluster} --region ${params.region}"
        sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
        sh 'chmod u+x ./kubectl' 
        sh './kubectl config --kubeconfig=/root/.kube/config view'
        sh 'printenv'
        sh './kubectl config current-context'
        sh 'cat $KUBECONFIG'
          sh './kubectl apply --kubeconfig $KUBECONFIG -f ./deployment/.'
      
        
      echo "success"
    }
  }

}
}
    