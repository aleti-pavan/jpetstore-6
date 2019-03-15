pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Build Started'
        sh '''mvn clean install -Dlicense.skip=true
'''
        echo 'Build Completed'
      }
    }
    stage('Testing Stage') {
      parallel {
        stage('SonarQube Test') {
          steps {
            echo 'SonarQube Test'
            sh 'mvn sonar:sonar -Dsonar.host.url=http://63.33.213.194:8081 -Dlicense.skip=true'
          }
        }
        stage('Print Tester Credentials') {
          steps {
            echo "The tester is ${TESTER}"
            sleep 15
          }
        }
        stage('Print build number') {
          steps {
            echo "this is build number ${BUILD_ID}"
          }
        }
      }
    }
    stage('JFrog Push') {
      steps {
        echo 'JFrog Push Started'
        script {
          def server = Artifactory.server "artifactory"
          def buildInfo = Artifactory.newBuildInfo()
          def rtMaven = Artifactory.newMavenBuild()

          rtMaven.tool = 'maven'
          rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'

          buildInfo = rtMaven.run pom: 'pom.xml', goals: "clean install -Dlicense.skip=true"
          buildInfo.env.capture = true
          buildInfo.name = 'jpetstore-6'
          server.publishBuildInfo buildInfo
        }

        echo 'JFrog Push Finished'
      }
    }
    stage('Deploy Prompt') {
      steps {
        input 'Deploy to Production?'
      }
    }
    stage('Deployment') {
      steps {
        echo 'Deployment Started'
        echo 'Deployment Finished'
      }
    }
  }
  tools {
    maven 'maven'
  }
  environment {
    TESTER = 'Pavan'
  }
}