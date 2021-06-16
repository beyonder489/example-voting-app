
pipeline {
  
   agent none

 

 stages{


     stage('worker-build'){
       agent {
         docker {
            image 'maven:3.8.1-adoptopenjdk-11'
            args '-v $HOME/.m2:/root/.m2'
         }
       }
       when{
           changeset "**/worker/**"
       }
       steps{
         echo 'Compiling worker app..' 
         dir('worker'){
           sh 'mvn compile'
         }
       }
     }



     stage('worker-test'){
       agent {
         docker { 
            image 'maven:3.8.1-adoptopenjdk-11'
            args '-v $HOME/.m2:/root/.m2'
         }
       }   
       when{
         changeset "**/worker/**"
       }
       steps{
         echo 'Running Unit Tets on worker app..'
         dir('worker'){
           sh 'mvn clean test'
           }
         }
     }


                             
     stage('worker-package'){
       agent {
         docker { 
            image 'maven:3.8.1-adoptopenjdk-11'
            args '-v $HOME/.m2:/root/.m2'
         }
       }
       when{
         branch 'master'
         changeset "**/worker/**"
       }
       steps{
         echo 'Packaging worker app'
         dir('worker'){
           sh 'mvn package -DskipTests'
           archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
         }
       }
     }
      


     stage('worker-docker-package'){
       agent any
       when{
         changeset "**/worker/**"
         branch 'master'
       }
       steps{
         echo 'Packaging worker app with Docker'
         script{
           docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
               def workerImage = docker.build("beyonder489/worker:v${env.BUILD_ID}", "./worker")
               workerImage.push()
               workerImage.push("${env.BRANCH_NAME}")
           }  
         }
       }
     }




     stage('result-build'){
       agent {
         docker {
            image 'node:8.9.0-alpine'
         }
       }
       when{
           changeset "**/result/**"
       }
       steps{
         echo 'Compiling result app..'
         dir('result'){
           sh 'npm install'
         }
       }
     }



     stage('result-test'){
       agent {
         docker {
            image 'node:8.9.0-alpine'
         }
       }
       when{
         changeset "**/result/**"
       }
       steps{
         echo 'Running Unit Tets on worker app..'
         dir('result'){
           sh 'npm install'
           sh 'npm test'
           }
         }
     }

 

     stage('result-docker-package'){
       agent any
       steps{
         echo 'Packaging result app with Docker'
         script{
           docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
               def workerImage = docker.build("beyonder489/result:v5, "./result")
               workerImage.push()
               workerImage.push("${env.BRANCH_NAME}")
           }
         }
       }

     }

     

     stage('vote-build'){
       agent {
         docker {
            image 'python:2.7.16-slim'
            args '--user root'
         }
       }
       steps{
         echo 'Compiling worker app..'
         dir('vote'){
           sh 'pip-install -r requirements.txt'
         }
       }
     }



     stage('vote-test'){
       agent {
         docker {
            image 'python:2.7.16-slim'
            args '--user root'
         }
       }
       steps{
         echo 'Running Unit Tets on vote app..'
         dir('vote'){
           sh 'pip-install -r requirements.txt'
           sh 'nosetests -v'
           }
         }
     }



//    stage('vote-docker-package'){
//      agent any
//       when{
//       }
//       steps{
//         echo 'Packaging vote app with Docker'
//         script{
//           docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
//               def workerImage = docker.build("beyonder489/vote:v${env.BUILD_ID}", "./vote")
//               workerImage.push()
//               workerImage.push("${env.BRANCH_NAME}")
//           }
//         }
//       }
//         
//     }
 }  



 post{
   always{
       echo "Pipeline for mono-pipe is complete..."
   }
 }

}
