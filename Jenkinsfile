pipeline {
  agent any
  stages {
     stage('Prepare Environment') {
      steps {
        sh 'cd ..'
        sh 'rm -rf SecurityShepherd_master*'
      }
    }
    
   stage('Fortify Build') {
      steps {
        sh 'sudo /opt/Fortify/Fortify_SCA_and_Apps_19.2.0/bin/sourceanalyzer -v -b 123456 src/*'
        sh 'sudo /opt/Fortify/Fortify_SCA_and_Apps_19.2.0/bin/sourceanalyzer -v -b 123456 -scan -f results.fpr'
        sh 'sudo cp /var/lib/jenkins/workspace/SecurityShepherd_master/results.fpr /var/lib/jenkins/workspace/deploy/results.fpr  '
      }
    }

    stage('Fortify - SAST') {
      agent {
        docker {
          image 'kondukto/kondukto-cli:dev'
          args '-v /var/lib/jenkins/workspace/deploy:/root -e KONDUKTO_HOST=${konduktoAddress} -e KONDUKTO_TOKEN=U1U2dlA4SmhFN1BaTFc3ZkRhVVVBTzNEakNtQlBNV3cweHlsaDB2Z284N0ROOURxRE9iUmJ1WlFRT0Jk'
        }
      }
      steps {
        sh 'kdt scan -p SecurityShepard -t fortify -b master -f /root/results.fpr'
      }
    }

    /*stage('SecurityScan - SCA') {
      agent {
        docker {
          image 'kondukto/kondukto-cli:dev'
          args '-e KONDUKTO_HOST=${konduktoAddress} -e KONDUKTO_TOKEN=U1U2dlA4SmhFN1BaTFc3ZkRhVVVBTzNEakNtQlBNV3cweHlsaDB2Z284N0ROOURxRE9iUmJ1WlFRT0Jk'
        }

      }
      steps {
        sh 'kdt scan -p SecurityShepard -t dependencycheck'
      }
    }
    */
    stage('Build') {
      agent {
        docker {
          image 'maven:3-alpine'
        }

      }
      steps {
        sh 'mvn package -DskipTests'
        
      }
    }
    
        stage('Deploy') {
                steps {
                sh 'sudo cp /var/lib/jenkins/workspace/SecurityShepherd_master@2/target/owaspSecurityShepherd.war /var/lib/jenkins/workspace/deploy/secshape.war'
              
                sh 'docker run --rm --name secshape -d -v /var/lib/jenkins/workspace/deploy:/usr/local/tomcat/webapps -p 9999:8080 tomcat:8.5'
      }
           
            

    }

  }
}
