pipeline {
    agent any
      tools {
          maven 'M2_HOME'
        //  jdk 'JAVA_HOME'
        }
    environment {
        DOCKER_IMAGE_NAME = "sandrarome/first-app:v1"
        DOCKER_USERNAME = "sandrarome"
        DOCKER_PASSWORD = credentials('DOCKER_SECRET')
    }
    stages {
        stage('Test') {
            steps {
                sh '''
                    echo "running the tests ......."
                    mvn clean test
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    echo "Building the docker image ......."
                    docker build -t "${DOCKER_IMAGE_NAME}" -f ./Dockerfile .
                '''
            }
        }
        stage('Push') {
            steps {
                sh '''
                    echo "Pushing docker image ......."
                    docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                    docker tag "${DOCKER_IMAGE_NAME}" "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker push "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker push "${DOCKER_IMAGE_NAME}":latest
                    echo "cleaning up the local images ......."
                    docker rmi "${DOCKER_IMAGE_NAME}":"$BUILD_NUMBER"
                    docker rmi "${DOCKER_IMAGE_NAME}":latest
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    echo "deploying the application ........"
                    docker rm -f metadata-service || true
                    docker run -d -p 8083:8080 --name metadata-service "${DOCKER_IMAGE_NAME}":latest
                '''
            }
        }
    }
}