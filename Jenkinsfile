pipeline {
  environment {
    imagename = "shivanjalikaname007/flask-hello-world"
    registryCredential = 'as-docker-hub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/devandsecops/flask-hello-world.git', branch: 'master'])

      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build imagename
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
             dockerImage.push('latest')

          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $imagename:$BUILD_NUMBER"
         sh "docker rmi $imagename:latest"

      }
    }
    stage('Deploy pod on k8s') {
      steps{
            sh "sudo scp /var/lib/jenkins/workspace/module/kubernetes.yaml ubuntu@ec2-52-26-11-149.us-west-2.compute.amazonaws.com:/home/ubuntu/"
            script{
                try{
                  sh "sudo ssh ubuntu@ec2-52-26-11-149.us-west-2.compute.amazonaws.com kubectl apply -f ."
                }catch(error){
                    sh "sudo ssh ubuntu@ec2-52-26-11-149.us-west-2.compute.amazonaws.com kubectl create -f ."
                }

      }
    }
   }
  }
}

