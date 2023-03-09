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
                sh 'docker build -t ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG .'
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

      stage ('Anmeldung und Push-Image auf Docker-Hub') {
          agent any
          steps {
             script {
               sh '''
                   echo $DOCKERHUB_PASSWORD | docker login -u ${DOCKERHUB_ID} --password-stdin
                   docker push ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
               '''
             }
          }
      }

    }   
}
