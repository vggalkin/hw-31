pipeline {
    // Задание 2: Указание метки агента
    agent { 
        label 'linux-agent' 
    }

    environment {
        // Пример переменной окружения
        BUILD_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Скачивание кода
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                // Компиляция (пример для Maven)
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                // Тестирование
                sh 'mvn test'
            }
            // Задание 4: Настройка отчетов (JUnit)
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deploy') {
            steps {
                // Развертывание (имитация)
                sh 'echo "Deploying version ${BUILD_VERSION} to staging..."'
                // Здесь может быть скрипт копирования артефактов или деплой в контейнер
            }
        }
    }

    post {
        always {
            // Очистка рабочего пространства
            cleanWs()
        }
        failure {
            // Уведомление об ошибке (можно настроить почту или Slack)
            echo 'Build failed! Check logs.'
        }
    }
}
