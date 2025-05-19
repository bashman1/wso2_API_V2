pipeline {
    agent any

    environment {
        // === Configuration ===
        GIT_REPO = 'http://localhost:3000/bash/WSO2-PRoJECT1.git'
        CREDENTIALS_ID = 'gogs-git-access'  // Replace with your Jenkins credentials ID
        CAR_FILE = 'target/ApiAssignment2_1.0.0.car'
        JDBC_JAR = 'C:\\Users\\HP\\Downloads\\postgresql-42.7.5.jar'
        CONTAINER_NAME = 'serviceOrchestration'
        IMAGE_NAME = 'wso2/wso2mi:4.4.0'

        // === Paths inside container ===
        MI_CAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/repository/deployment/server/carbonapps/ApiAssignment2_1.0.0.car'
        MI_JAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/lib/postgresql-42.7.5.jar'
    }

    stages {
        // stage('Clone from Gogs') {
        //     steps {
        //         git credentialsId: "${CREDENTIALS_ID}", url: "${GIT_REPO}"
        //     }
        // }

        stage('Build Project') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Run WSO2 MI in Docker') {
            steps {
                script {
                    // Ensure clean container start
                    bat "docker stop ${CONTAINER_NAME} || exit 0"
                    bat "docker rm ${CONTAINER_NAME} || exit 0"

                    // Compose Docker run command
                    def carMount = "${env.WORKSPACE}\\${CAR_FILE}"
                    def jdbcMount = "${JDBC_JAR}"

                    bat """
                        docker run -d ^
                            -p 8290:8290 -p 8253:8253 ^
                            --name ${CONTAINER_NAME} ^
                            -v "${carMount}:${MI_CAR_PATH}" ^
                            -v "${jdbcMount}:${MI_JAR_PATH}" ^
                            ${IMAGE_NAME}
                    """
                }
            }
        }

        stage('Health Check') {
            steps {
                bat "curl http://localhost:8290/health || echo 'Health endpoint not reachable (optional)'"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            bat "docker stop ${CONTAINER_NAME} || exit 0"
            bat "docker rm ${CONTAINER_NAME} || exit 0"
        }
    }
}
