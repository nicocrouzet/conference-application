pipeline {
    agent any
    tools {
        maven "maven 3.6"
    }
     environment {
        NEXUS_ARTIFACT_VERSION= "${env.BUILD_NUMBER}"
    }

    options {
        parallelsAlwaysFailFast()
    }
    stages {
        stage('Non-Parallel Stage') {
            steps {
                echo 'This stage will be executed first.'
            }
        }
        stage('Parallel Stage') {
            parallel {
                   stage('Checkstyle') {
                        steps{
                            // Run the maven build with checkstyle
                            echo "remove checkstyle because too long"
                            sh "mvn clean package checkstyle:checkstyle"
                         }
                     }
                    stage('Sonarqube') {
                        steps {
                            withSonarQubeEnv('SonarQube') {
//                            sh "mvn  clean package sonar:sonar -Dsonar.host_url=$SONAR_HOST_URL "
                            }
                         }
                    }
            }
        }
        stage('Build image') {
            steps{
                script{
                    def customImage = docker.build("conference-app")
                }
            }
        }

        stage('Run Test image Parallel 1') {
            parallel {
                   stage('Run 1') {
                        steps{
                            // Run the maven build with checkstyle
                            echo "Run parallel 1"
                            sh "docker stop conference-app-test || true && docker rm conference-app-test || true"
                            sh 'docker run -d --name conference-app-test -p 8890:8080 conference-app'
                         }
                     }
                   stage('Run 2') {
                        steps{
                            // Run the maven build with checkstyle
                            echo "Run parallel 2"
                         }
                     }
                   stage('Run 3') {
                        steps{
                            // Run the maven build with checkstyle
                            echo "Run parallel 3"
                         }
                   }
            }
        }
    }
}
