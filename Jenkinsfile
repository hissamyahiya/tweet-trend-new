pipeline {
    agent {
       node {
         label "maven"
      }
    }
environment {
    PATH= "/opt/apache-maven-3.9.6/bin:$PATH"
}
    stages {
        stage("build") {
            steps {
                echo "##########**&&&&---- Build Started Hissam-------&&&&***###########"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "##########**&&&&----Build Completed Hissam-------&&&&***###########"
            }
        }

        stage("test") {
            steps {
                 echo "##########**&&&&----Unit Test Started Hissam-------&&&&***###########"
                sh 'mvn surefire-report:report'
                 echo "##########**&&&&----Unit Test Completed Hissam-------&&&&***###########"
            }
        }
        stage('SonarQube analysis') {
            environment {
              scannerHome = tool 'hissam-sonar-scanner';
            }
            steps {
           withSonarQubeEnv('hissam-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
            sh "${scannerHome}/bin/sonar-scanner"
           }
        }
       }
    }
}