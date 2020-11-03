pipeline {
  parameters {
    choice(name: 'maven_version', choices: ['3.6.3-jdk-11', '3.6.3-jdk-11-openj9'], description: 'Maven version')
  }
  agent {
      docker {
          image "maven:${params.maven_version}"
          args "--user root"
          alwaysPull true
          label 'slave'
      }
  }
  stages {
    stage('Building image') {
      steps{
        script {
          sh "mvn package"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {        
        withCredentials([usernamePassword(credentialsId: '4b01ae84-9925-44ee-bf60-f37140c908de', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASSWORD')]) {
        sh """
          CURL_RESPONSE=\$(curl -v -u $TOMCAT_USER:$TOMCAT_PASSWORD -T target/*.war "http://34.71.197.217/manager/text/deploy?path=/hello2&update=true")    
          if [[ \$CURL_RESPONSE == *"FAIL"* ]]; then
            echo "war deployment failed"
            exit 1
          else    
            echo "war deployed successfully "
            exit 0
          fi
        """
          }
        }
      }
    }
  }
}
