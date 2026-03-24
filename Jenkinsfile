pipeline {
    agent { 
        label 'linux-agent' 
    }

    tools {
        maven 'Maven-3.9.8'
    }

    environment {
        BUILD_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📥 Скачивание кода...'
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                echo '🔨 Компиляция...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Запуск тестов...'
                sh 'mvn test'
            }
            post {
                always {
                    script {
                        // Проверка наличия отчётов без findFiles
                        def hasReports = sh(
                            script: 'test -d target/surefire-reports && ls target/surefire-reports/TEST-*.xml 1>/dev/null 2>&1 && echo "1" || echo "0"',
                            returnStdout: true
                        ).trim()
                        
                        if (hasReports == '1') {
                            echo '📊 Публикация JUnit-отчётов...'
                            junit 'target/surefire-reports/*.xml'
                        } else {
                            echo '⚠️ No JUnit reports found. Check test structure.'
                        }
                    }
                }
            }
        }

        stage('Package') {
            steps {
                echo '📦 Сборка артефакта...'
                sh 'mvn package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Развертывание...'
                sh '''
                    echo "Deploying $BUILD_VERSION at $(date)" > deploy_log.txt
                    cat deploy_log.txt
                    ls -lh target/*.jar 2>/dev/null || true
                '''
            }
        }
    }

    post {
        always {
            echo '🧹 Очистка...'
            cleanWs()
            // Архивация артефактов (опционально)
            archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
        }
        failure {
            echo '❌ BUILD FAILED'
        }
        success {
            echo '✅ BUILD SUCCESS'
        }
    }
}
