def registry = 'https://hissam01.jfrog.io'
def imageName = 'hissam01.jfrog.io/hissam-docker-local/ttrend'
def version = '2.1.3'
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
    /*   stage('SonarQube analysis') {
            environment {
              scannerHome = tool 'hissam-sonar-scanner';
            }
            steps {
           withSonarQubeEnv('hissam-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
            sh "${scannerHome}/bin/sonar-scanner"
           }
        }
       }

       stage("Quality Gate") {
          steps {
            script {
      timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
            }
          }
}  */

        stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"JFrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "maven-hissam-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }

        stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

            stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'JFrog-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }
     stage ("Deploy using  helm") {
        steps {
            script {
                echo '<------------Helm Install started---------------->'
              sh 'helm install ttrend ttrend-0.1.0.tgz'
            }
        }
     }
    }
}