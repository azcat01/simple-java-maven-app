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
        // sleep(time: 1, unit: 'MINUTES')
      }
    }
    stage('Run in AWS') {
      steps {
        withAWS(credentials: 'react-app-server', region:'ap-southeast-1') {
          createDeployment(
            gitHubRepository: 'azcat01/simple-java-maven-app',
            gitHubCommitId: 'f5fd67abd205e869375707179516068ed7d1936b',
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
//     stage('Run in AWS') {
//       withAWS(credentials: 'react-app-server', region:'ap-southeast-1') {
//         createDeployment(
//           gitHubRepository: 'azcat01/simple-java-maven-app',
//           gitHubCommitId: 'b23d8591a9d3f0dc3e4dc41bb258c5007d9be806',
//           applicationName: 'simple-java-maven-app',
//           deploymentGroupName: 'CodeDeploy-maven-app',
//           deploymentConfigName: 'CodeDeployDefault.AllAtOnce',
//           description: 'Deployment from Jenkins Local',
//           waitForCompletion: 'true'
//         )
//       }
//     }
//   }
// }