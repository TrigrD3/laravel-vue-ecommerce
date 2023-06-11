pipeline {
    agent any
    stages {
        stage("Verify tooling") {
            steps {
                sh '''
                    docker info
                    docker version
                    docker compose version
                '''
            }
        }     
        stage("Clear all running docker containers") {
            steps {
                script {
                    try {
                        sh 'docker rm -f $(docker ps -a -q)'
                    } catch (Exception e) {
                        echo 'No running container to clear up...'
                    }
                }
            }
        }
        stage("Start Docker") {
            steps {
                sh 'make up'
                sh 'docker compose ps'
            }
        }

        stage("Populate .env file") {
            steps {
                dir("/var/lib/jenkins/workspace/envs/laravel-vue") {
                    script {
                        sh 'cp .env ${WORKSPACE}'
                    }
                }
            }
        }

        stage("Run Composer Install && update") {
            steps {
                sh 'docker compose run --rm composer install'
                sh 'docker compose run --rm composer update'
            }
        }

        stage("Generate Laravel Key") {
            steps {
                script {
                    sh 'docker compose exec php php artisan key:generate'
                }
            }
        }

        stage("Run Database Migrations") {
            steps {
                script {
                    sh 'docker compose exec php php artisan migrate'
                }
            }
        }

        stage("List containers") {
            steps {
                script {
                    sh 'docker compose ps'
                }
            }
        }
              
        // stage("Run Tests") {
        //     steps {
        //         sh 'docker compose exec php php artisan test'
        //     }
        // }
    }


    // post {
    //     always {
    //         sh 'docker compose down --remove-orphans -v'
    //         sh 'docker compose ps'
    //     }
    // }
}