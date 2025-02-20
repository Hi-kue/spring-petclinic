pipeline {
    agent any

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
                        checkout([$class: 'GitSCM',
                            branches: [[name: '*/main']],
                            userRemoteConfigs: [[url: 'https://github.com/Hi-kue/spring-petclinic.git']],
                            extensions: [[$class: 'CleanBeforeCheckout']]
                        ])
                    } catch (Exception e) {
                        error "Failed to checkout repository: ${e.getMessage()}"
                    }
                }
        }

        stage('Build') {
            steps {
                echo "Building w/Maven..."
                sh './mvnw clean package'
            }
        }

        stage('Jacoco Test') {
            steps {
                echo "Testing w/Jacoco (Building Coverage)..."
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
    }

    post {
        success {
            echo 'Pipeline has stopped and completed successfully.'
        }
        failure {
            echo 'Something went wrong, pipeline has failed.'
        }
    }
}