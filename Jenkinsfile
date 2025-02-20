pipeline {
    agent any

    triggers {
        cron('H/10 * * * 1')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Hi-kue/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Testing with Jacoco') {
            steps {
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