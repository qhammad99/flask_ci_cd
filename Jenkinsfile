pipeline {

  environment {
    dockerimagename = "agachan/lb324"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/qhammad99/flask_ci_cd.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("v1")
          }
        }
      }
    }
    stage('Deploying App to Kubernetes') {
      steps {
        script {
        //   kubernetesDeploy(configs: "cluster_deployment.yaml", kubeconfigId: "kubernetes")
          withKubeConfig([credentialsId: "kubernetes"]) {
            sh 'kubectl apply -f cluster_deployment.yaml'
          }
        }
      }
    }

  }

}