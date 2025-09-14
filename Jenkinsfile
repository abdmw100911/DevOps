pipeline {
    agent any
    
    tools {
        jdk 'JAVA_HOME'       // Make sure Jenkins has a JDK tool named JAVA_HOME (pointing to JDK 21)
        maven 'Maven-3.9'     // This should match the Maven name you set in Jenkins (not MAVEN_HOME)
    }
    
    environment {
        WAR_FILE   = 'C:/Users/Admin/.jenkins/workspace/DevOps-Pipeline/target/DevOps.war'
        TOMCAT_URL = 'http://localhost:8081'
    }
    
    stages {
        stage('Clean Project') {
            steps {
                bat "mvn clean"
            }
        }

        stage('Build Project') {
            steps {
                bat "mvn package -DskipTests"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFilePath = "${WAR_FILE}"
                    echo "WAR file path: ${warFilePath}"

                    if (fileExists(warFilePath)) {
                        echo 'WAR file found, deploying...'

                        withCredentials([usernamePassword(credentialsId: 'tomcat-creds',
                                                          usernameVariable: 'TOMCAT_USER',
                                                          passwordVariable: 'TOMCAT_PASSWORD')]) {
                            bat """
                                curl --upload-file "${warFilePath}" ^
                                --user %TOMCAT_USER%:%TOMCAT_PASSWORD% ^
                                "${TOMCAT_URL}/manager/text/deploy?path=/devops&update=true"
                            """
                        }
                    } else {
                        error('WAR file not found!')
                    }
                }
            }
        }
    } // end stages

    post {
        always {
            echo 'Build completed'
        }
    }
} // end pipeline
