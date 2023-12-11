pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            // sh(script: 'docker compose build')
            pwsh(script: 'docker images -a')
            pwsh(script: """
               cd azure-vote/
               docker images -a
               docker build -t jenkins-pipeline .
               docker images -a
               cd ..
            """)

         }
      }
      stage('Start test app') {
         steps {
            pwsh(script: """
            docker-compose up -d
            scripts/test_container.ps1
            """)
         }
         post {
            success {
               echo "App started succeddfuly :)"
            } 
            failure {
               echo "App failed to start :("
            }
         }
      }
      // stage('Run Tests') { 
      //    steps {
      //       pwsh(script: """
      //       pytest ./tests/test_sample.py
      //       """)
      //    }
      // }
      stage('Stop test app') {
         steps{
            pwsh(script: """
            docker-compose down
            """)
         }
      }
      stage('Push container') {
         steps {
            echo "Workspace is $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               docker.withRegistry('https://index.docker.io/v1/', 'DockerHub') {
                  def image = docker.build('jenkins-course/jenkins-course:latest')
                  image.push()
               }
            }
         }
      }
   }

}
