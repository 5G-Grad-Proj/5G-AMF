pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }



      stage('Free5gc') {
         parallel {
            stage('Run free5gc-amf') {
               steps {
                  echo "command"

               }
            }
            stage('Run free5gc-smf') {
               steps {
                  echo "command"
               }
            }
         }
         }






      stage('Docker Build') {
         steps {
               sh(script: """
                  cd Dockerfile/
                 
                  cd nf_amf/
                   docker images -a
                   docker build -t gradproj/nf-amf . 
                   docker images -a 

                                    
            """)
         }
      }
      stage ('Push Docker image'){

            steps{
                echo "============Pushing Docker image==========="
             withCredentials([usernamePassword(credentialsId: 'DockerHUB', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])
                {
                sh """
                    docker login -u ${USERNAME} -p ${PASSWORD}
                    docker push gradproj/amf:latest
                    
                    """ 
                }
            }

    }

      stage('Deploy'){
            steps {
                 sh 'kubectl apply -f deployment.yaml'
            }
        }

    }

    }


