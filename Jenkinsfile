node {
  docker.image('maven:3.9.0-eclipse-temurin-11').inside('-v /root/.m2:/root/.m2 -v "$HOME":/home') {
    stage('Build') {
      sh 'mvn -B -DskipTests clean package'
    }
    stage('Test') {
      try {
        sh 'mvn test'
      } finally {
          junit 'target/surefire-reports/*.xml'
      }
    }
    stage('Deliver') {
      sh './jenkins/scripts/deliver.sh'
    }
  }
}

// pipeline {
//     agent {
//         docker {
//             image 'maven:3.9.0-eclipse-temurin-11' 
//             args '-v /root/.m2:/root/.m2' 
//         }
//     }
//     stages {
//         stage('Build') { 
//             steps {
//                 sh 'mvn -B -DskipTests clean package' 
//             }
//         }
//     }
// }
