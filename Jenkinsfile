pipeline {

  agent any
  tools { 
      maven 'Maven' 
    } 
  stages {
    stage('Compile stage') {
    steps {
      sh '''
        mvn -version && \
        cd java-web-project && \
        mvn package && \
        mvn clean compile
        '''
      }
    }

    stage('Testing stage') {
    steps {

        sh "mvn test"
    }
    }
    stage('Final Jenkins YAML Pipeline Stage') {
    steps {

      sh "echo 'Jenkins Pipeline Complete'"
      }
    }
  }
}
