pipeline {
    agent any
    
    triggers {
        cron('H/10 * * * 1')
    }
    
    options {
        skipDefaultCheckout()
    }
    
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
                echo 'Building with Maven...'
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests with Jacoco coverage...'
                sh './mvnw test'
            }
            post {
                always {
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java',
                        exclusionPattern: '**/model/*'
                    )
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