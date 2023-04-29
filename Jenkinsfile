// Declarative Pipeline //

pipeline {
  agent {
    docker {
      image 'maven:3.9.0-eclipse-temurin-11' 
      args '-v /root/.m2:/root/.m2 -v "$HOME":/home'
    }
  }
  options {
    skipStagesAfterUnstable()
  }
  stages {
    stage('Build') { 
      steps {
        sh 'mvn -B -DskipTests clean package' 
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }
    stage('Manual Approval') {
      steps {
        input(message: 'Lanjutkan ke tahap Deploy?')
      }
    }
    stage('Deliver') {
      steps {
        sh './jenkins/scripts/deliver.sh'
        sleep(time: 1, unit: 'MINUTES')
      }
    }
    stage('Run in AWS') {
      withAWS(credentials: 'react-app-server', region:'ap-southeast-1') {
        createDeployment(
          gitHubRepository: 'azcat01/simple-java-maven-app',
          gitHubCommitId: '4361ed15897fe3b3adfc1d17a9c7b11e6c3c96c2',
          applicationName: 'simple-java-maven-app',
          deploymentGroupName: 'CodeDeploy-maven-app',
          deploymentConfigName: 'CodeDeployDefault.AllAtOnce',
          description: 'Deployment from Jenkins Local',
          waitForCompletion: 'true'
        )
      }
    }
  }
}

// Scripted Pipeline //

// node {
//   docker.image('maven:3.9.0-eclipse-temurin-11').inside('-v /root/.m2:/root/.m2 -v "$HOME":/home') {
//     stage('Build') {
//       sh 'mvn -B -DskipTests clean package'
//     }
//     stage('Test') {
//       try {
//         sh 'mvn test'
//       } finally {
//           junit 'target/surefire-reports/*.xml'
//       }
//     }
//     stage('Manual Approval') {
//       input(message: 'Lanjutkan ke tahap Deploy?')
//     }
//     stage('Deliver') {
//       sh './jenkins/scripts/deliver.sh'
//       sleep(time: 1, unit: 'MINUTES')
//     }
//   }
// }