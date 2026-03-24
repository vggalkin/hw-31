pipeline {
    agent { 
        label 'linux-build-agent' 
    }

    tools {
        maven 'Maven-3.9.80'
    }

    environment {
        BUILD_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Скачивание кода из репозитория...'
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                echo 'Компиляция проекта...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo 'Запуск тестов...'
                sh 'mvn test'
            }
            post {
                always {
                    // Публикация отчётов только если файлы существуют
                    script {
                        def reports = findFiles(glob: 'target/surefire-reports/*.xml')
                        if (reports && reports.length > 0) {
                            junit 'target/surefire-reports/*.xml'
                        } else {
                            echo '⚠️ Warning: No JUnit reports found.'
                        }
                    }
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Сборка JAR артефакта...'
                sh 'mvn package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Развертывание приложения...'
                // Исправлено: используем $BUILD_NUMBER вместо ${env.BUILD_NUMBER}
                sh '''
                    echo "Deploying version $BUILD_VERSION at $(date)" > deploy_log.txt
                    cat deploy_log.txt
                    ls -lh target/*.jar 2>/dev/null || true
                '''
            }
        }
    }

    post {
        always {
            echo 'Очистка рабочего пространства...'
            cleanWs()
        }
        failure {
            echo '❌ Сборка не удалась! Проверьте логи.'
        }
        success {
            echo '✅ Сборка успешно завершена!'
        }
    }
}
