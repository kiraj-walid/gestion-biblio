pipeline {
    agent any
    
    tools {
        maven 'Maven-3.9'
    }
    
    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        GITHUB_CREDENTIALS_ID = 'git'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: "${GITHUB_CREDENTIALS_ID}",
                    url: 'https://github.com/kiraj-walid/gestion-biblio.git'
            }
        }
        
        stage('Build Discovery Service') {
            steps {
                dir('ms-discovery') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Config Service') {
            steps {
                dir('ms-config') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Livre Service') {
            steps {
                dir('ms-livre') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build User Service') {
            steps {
                dir('ms-etudiant') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Emprunt Service') {
            steps {
                dir('ms-emprunt') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Gateway Service') {
            steps {
                dir('ms-gateway') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                parallel(
                    discovery: {
                        dir('ms-discovery') {
                            sh 'mvn test'
                        }
                    },
                    config: {
                        dir('ms-config') {
                            sh 'mvn test'
                        }
                    },
                    livre: {
                        dir('ms-livre') {
                            sh 'mvn test'
                        }
                    },
                    user: {
                        dir('ms-etudiant') {
                            sh 'mvn test'
                        }
                    },
                    emprunt: {
                        dir('ms-emprunt') {
                            sh 'mvn test'
                        }
                    },
                    gateway: {
                        dir('ms-gateway') {
                            sh 'mvn test'
                        }
                    }
                )
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose down || true'
                    sh 'docker-compose up -d'
                }
            }
        }
        
        stage('Health Check') {
            steps {
                script {
                    sleep(time: 60, unit: 'SECONDS')
                    sh '''
                        curl -f http://localhost:8761/actuator/health || exit 1
                        curl -f http://localhost:9999/actuator/health || exit 1
                        curl -f http://localhost:8888/actuator/health || exit 1
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline exécuté avec succès!'
        }
        failure {
            echo 'Le pipeline a échoué!'
        }
    }
}