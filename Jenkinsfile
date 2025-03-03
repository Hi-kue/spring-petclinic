pipeline {
    agent any

    environment {
        MAVEN_OPTS = "-Dmaven.repo.local=/var/jenkins_home/.m2/repository"
    }

    triggers {
        cron('H/10 * * * 1')
    }

    options {
        skipDefaultCheckout()
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    try {
                        checkout scm: [
                            $class: 'GitSCM',
                            branches: [[name: '*/main']],
                            userRemoteConfigs: [[url: 'https://github.com/Hi-kue/spring-petclinic.git']],
                            extensions: [[
                                $class: 'CleanBeforeCheckout'
                            ]]
                        ]
                    } catch (Exception e) {
                        error "Failed to checkout repository: ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building w/Maven...'
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }
        
        stage('Jacoco Test Coverage') {
            steps {
                echo 'Running Tests with Jacoco Coverage...'
                sh './mvnw test'
            }
            post {
                success {
                    step([
                        $class: 'JacocoPublisher',
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java',
                        exclusionPattern: '**/test/**,**/model/**'
                    ])
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
