pipeline {
    agent any

    environment {
        CAR_FILE = 'target/ApiAssignment2_1.0.0.car'
        JDBC_JAR = 'C:\\Users\\HP\\Downloads\\postgresql-42.7.5.jar'
        CONTAINER_NAME = 'serviceOrchestration'
        IMAGE_NAME = 'wso2/wso2mi:4.4.0'

        MI_CAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/repository/deployment/server/carbonapps/ApiAssignment2_1.0.0.car'
        MI_JAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/lib/postgresql-42.7.5.jar'
    }

    stages {
        stage('Build Project') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Run WSO2 MI in Docker') {
            steps {
                script {
                    bat "docker stop ${CONTAINER_NAME} || exit 0"
                    bat "docker rm ${CONTAINER_NAME} || exit 0"

                    bat """
                        docker run -d ^
                            -p 8290:8290 -p 8253:8253 ^
                            --name ${CONTAINER_NAME} ^
                            -v "${env.WORKSPACE}\\${CAR_FILE}:${MI_CAR_PATH}" ^
                            -v "${JDBC_JAR}:${MI_JAR_PATH}" ^
                            ${IMAGE_NAME}
                    """
                }
            }
        }


        stage('Health Check') {
    steps {
        bat 'ping 127.0.0.1 -n 10 > nul'

        bat '''
cmd /V:ON /C " ^
set RETRIES=5 && ^
set DELAY=10 && ^
for /L %%i in (1,1,!RETRIES!) do ( ^
    echo Attempt %%i: Checking service... && ^
    curl -s -o nul -w %%{http_code} http://localhost:8290/wso2apidemo/createCustomer > status.txt && ^
    set /p STATUS=<status.txt && ^
    echo HTTP status: !STATUS! && ^
    if \"!STATUS!\" == \"200\" ( ^
        echo Health check passed. && ^
        exit /b 0 ^
    ) && ^
    timeout /T !DELAY! > nul ^
) && ^
echo Health check failed after !RETRIES! attempts. && ^
exit /b 1"
'''

        // This will help you debug the container logs after failure
        bat "docker logs ${CONTAINER_NAME}"
    }
}

    }

    post {
        always {
            echo 'Cleaning up Docker...'
            // bat "docker stop ${CONTAINER_NAME} || exit 0"
            // bat "docker rm ${CONTAINER_NAME} || exit 0"
        }
    }
}


