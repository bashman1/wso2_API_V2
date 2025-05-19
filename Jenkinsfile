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
            bat "docker stop ${CONTAINER_NAME} || exit 0"
            bat "docker rm ${CONTAINER_NAME} || exit 0"
        }
    }
}


// pipeline {
//     agent any

//     environment {
//         // Configuration
//         GIT_REPO = 'http://localhost:3000/bash/WSO2-PRoJECT1.git'
//         CREDENTIALS_ID = 'gogs-git-access'  // Replace this with your actual Jenkins credentials ID

//         CAR_FILE = 'target/ApiAssignment2_1.0.0.car'
//         JDBC_JAR = 'C:\\Users\\HP\\Downloads\\postgresql-42.7.5.jar'
//         CONTAINER_NAME = 'serviceOrchestration'
//         IMAGE_NAME = 'wso2/wso2mi:4.4.0'

//         // Paths inside container
//         MI_CAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/repository/deployment/server/carbonapps/ApiAssignment2_1.0.0.car'
//         MI_JAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/lib/postgresql-42.7.5.jar'
//     }

//     stages {
//         stage('Clone from Gogs') {
//             steps {
//                 git credentialsId: "${CREDENTIALS_ID}", url: "${GIT_REPO}"
//             }
//         }

//         stage('Build Project') {
//             steps {
//                 bat 'mvn clean install'
//             }
//         }

//         stage('Run WSO2 MI in Docker') {
//             steps {
//                 script {
//                     // Stop and remove if exists
//                     bat "docker stop ${CONTAINER_NAME} || exit 0"
//                     bat "docker rm ${CONTAINER_NAME} || exit 0"

//                     // Run new container
//                     bat """
//                         docker run -d ^
//                             -p 8290:8290 -p 8253:8253 ^
//                             --name ${CONTAINER_NAME} ^
//                             -v "${env.WORKSPACE}\\${CAR_FILE}:${MI_CAR_PATH}" ^
//                             -v "${JDBC_JAR}:${MI_JAR_PATH}" ^
//                             ${IMAGE_NAME}
//                     """
//                 }
//             }
//         }

//         stage('Health Check') {
//             steps {
//                 // Wait for MI to start fully
//                 bat 'timeout /T 20 /NOBREAK'
//                 // Call your deployed endpoint
//                 bat 'curl http://localhost:8290/wso2apidemo/createCustomer || echo "Health check failed (optional)"'
//             }
//         }
//     }

//     post {
//         always {
//             echo 'Cleaning up Docker...'
//             bat "docker stop ${CONTAINER_NAME} || exit 0"
//             bat "docker rm ${CONTAINER_NAME} || exit 0"
//         }
//     }
// }


// // pipeline {
// //     agent any

// //     environment {
// //         CAR_FILE = 'target/ApiAssignment2_1.0.0.car'
// //         JDBC_JAR = 'C:\\Users\\HP\\Downloads\\postgresql-42.7.5.jar'
// //         CONTAINER_NAME = 'serviceOrchestration'
// //         IMAGE_NAME = 'wso2/wso2mi:4.4.0'

// //         MI_CAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/repository/deployment/server/carbonapps/ApiAssignment2_1.0.0.car'
// //         MI_JAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/lib/postgresql-42.7.5.jar'
// //     }

// //     stages {
// //         stage('Build Project') {
// //             steps {
// //                 bat 'mvn clean install'
// //             }
// //         }

// //         stage('Run WSO2 MI in Docker') {
// //             steps {
// //                 script {
// //                     bat "docker stop ${CONTAINER_NAME} || exit 0"
// //                     bat "docker rm ${CONTAINER_NAME} || exit 0"

// //                     bat """
// //                         docker run -d ^
// //                             -p 8290:8290 -p 8253:8253 ^
// //                             --name ${CONTAINER_NAME} ^
// //                             -v "${env.WORKSPACE}\\${CAR_FILE}:${MI_CAR_PATH}" ^
// //                             -v "${JDBC_JAR}:${MI_JAR_PATH}" ^
// //                             ${IMAGE_NAME}
// //                     """
// //                 }
// //             }
// //         }

// //         stage('Health Check') {
// //             steps {
// //                 bat "curl http://localhost:8290/health || echo 'Health endpoint not reachable (optional)'"
// //             }
// //         }
// //     }

// //     post {
// //         always {
// //             echo 'Cleaning up...'
// //             bat "docker stop ${CONTAINER_NAME} || exit 0"
// //             bat "docker rm ${CONTAINER_NAME} || exit 0"
// //         }
// //     }
// // }







// // // pipeline {
// // //     agent any

// // //     environment {
// // //         // === Configuration ===
// // //         GIT_REPO = 'http://localhost:3000/bash/WSO2-PRoJECT1.git'
// // //         CREDENTIALS_ID = 'gogs-git-access'  // Replace with your Jenkins credentials ID
// // //         CAR_FILE = 'target/ApiAssignment2_1.0.0.car'
// // //         JDBC_JAR = 'C:\\Users\\HP\\Downloads\\postgresql-42.7.5.jar'
// // //         CONTAINER_NAME = 'serviceOrchestration'
// // //         IMAGE_NAME = 'wso2/wso2mi:4.4.0'

// // //         // === Paths inside container ===
// // //         MI_CAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/repository/deployment/server/carbonapps/ApiAssignment2_1.0.0.car'
// // //         MI_JAR_PATH = '/home/wso2carbon/wso2mi-4.4.0/lib/postgresql-42.7.5.jar'
// // //     }

// // //     stages {
// // //         // stage('Clone from Gogs') {
// // //         //     steps {
// // //         //         git credentialsId: "${CREDENTIALS_ID}", url: "${GIT_REPO}"
// // //         //     }
// // //         // }

// // //         stage('Build Project') {
// // //             steps {
// // //                 bat 'mvn clean install'
// // //             }
// // //         }

// // //         stage('Run WSO2 MI in Docker') {
// // //             steps {
// // //                 script {
// // //                     // Ensure clean container start
// // //                     bat "docker stop ${CONTAINER_NAME} || exit 0"
// // //                     bat "docker rm ${CONTAINER_NAME} || exit 0"

// // //                     // Compose Docker run command
// // //                     def carMount = "${env.WORKSPACE}\\${CAR_FILE}"
// // //                     def jdbcMount = "${JDBC_JAR}"

// // //                     bat """
// // //                         docker run -d ^
// // //                             -p 8290:8290 -p 8253:8253 ^
// // //                             --name ${CONTAINER_NAME} ^
// // //                             -v "${carMount}:${MI_CAR_PATH}" ^
// // //                             -v "${jdbcMount}:${MI_JAR_PATH}" ^
// // //                             ${IMAGE_NAME}
// // //                     """
// // //                 }
// // //             }
// // //         }

// // //         stage('Health Check') {
// // //             steps {
// // //                 bat "curl http://localhost:8290/health || echo 'Health endpoint not reachable (optional)'"
// // //             }
// // //         }
// // //     }

// // //     post {
// // //         always {
// // //             echo 'Cleaning up...'
// // //             bat "docker stop ${CONTAINER_NAME} || exit 0"
// // //             bat "docker rm ${CONTAINER_NAME} || exit 0"
// // //         }
// // //     }
// // // }
