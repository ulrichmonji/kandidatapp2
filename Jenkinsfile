pipeline {
    environment {
        IMAGE_NAME = "kandidatapp2"
        IMAGE_TAG = "R03"
        DOCKERHUB_ID = "royem001"
        DOCKERHUB_PASSWORD = credentials('dockerhub_password')
        HOST_IP = "${HOST_IP_PARAM}"
        HOST_PORT = "${HOST_PORT_PARAM}"
    }
    agent none
    stages {
       stage('Build image') {
           agent any
           steps {
              script {
                sh '''
                  docker build -t ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG .
                  # docker tag ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG ${DOCKERHUB_ID}/${IMAGE_NAME}:${GIT_BRANCH}-${GIT_COMMIT} 
                  '''
              }
           }
       }
       stage('Run container') {
          agent any
          steps {
            script {
              sh '''
                  docker rm -f $IMAGE_NAME
                  docker run --name $IMAGE_NAME -d -p $HOST_PORT:80 ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
                  sleep 5
              '''
             }
          }
       }

       stage('Test Login') {
          when {
             expression { GIT_BRANCH == 'origin/Login' }
           }
           agent any
           steps {
              script {
                sh '''
                    echo "CODE DE TEST du login"
                   '''
              }
           }
       }
     
       stage('Test Logout') {
          when {
             expression { GIT_BRANCH == 'origin/Logout' }
           }
           agent any
           steps {
              script {
                sh '''
                    echo "CODE DE TEST du logout"
                   '''
              }
           }
       }
       stage('Test Register') {
          when {
             expression { GIT_BRANCH == 'origin/Register' }
           }
           agent any
           steps {
              script {
                sh '''
                    echo "CODE DE TEST du Register"
                   '''
              }
           }
       }            
       stage('Test image') {
           agent any
           steps {
              script {
                sh '''
                    curl ${HOST_IP}:${HOST_PORT} -I  | grep -i 302
                   '''
              }
           }
       }
       stage('Clean container') {
          agent any
          steps {
             script {
               sh '''
                   docker stop $IMAGE_NAME
                   docker rm $IMAGE_NAME
               '''
             }
          }
      }
/*
      stage('Create release') {
        script {
            if (env.BRANCH_NAME == 'origin/Login') 
                {
                sh "docker tag ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG ${DOCKERHUB_ID}/${IMAGE_NAME}:${GIT_BRANCH}-${GIT_COMMIT} ""

                }
            if (env.BRANCH_NAME == 'origin/Logout') 
                {
                echo 'Hello from null branch'
                }
            if (env.BRANCH_NAME == 'origin/Register') 
                {
                echo 'Hello from null branch'
                }                
            else {
                sh "echo 'Hello from ${env.BRANCH_NAME} branch!'"
                }
            }
      }
*/

      stage ('Anmeldung und Push-Image auf Docker-Hub') {
          agent any
          steps {
             script {
               sh '''
                   echo $DOCKERHUB_PASSWORD | docker login -u ${DOCKERHUB_ID} --password-stdin
                   docker tag ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG ${DOCKERHUB_ID}/${IMAGE_NAME}:${GIT_BRANCH}-${GIT_COMMIT} 
                   # docker push ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
                   docker push ${DOCKERHUB_ID}/${IMAGE_NAME}:${GIT_BRANCH}-${GIT_COMMIT} 
               '''
             }
          }
      }

    }   
}
