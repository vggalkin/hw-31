pipeline {
    // Задание 2: Указание метки агента (замените на вашу метку)
    agent { 
        label 'linux-agent' 
    }

    tools {
        maven 'Maven-3.9.8' // Убедитесь, что Maven настроен в Jenkins (Manage Jenkins -> Global Tool Configuration)
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
                    // Задание 4: Публикация отчетов JUnit
                    junit 'target/surefire-reports/*.xml'
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
                // Имитация деплоя: создание файла с версией
                sh '''
                    echo "Deploying version ${env.BUILD_NUMBER} at $(date)" > deploy_log.txt
                    cat deploy_log.txt
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
            echo 'Сборка не удалась! Проверьте логи тестов.'
        }
    }
}
