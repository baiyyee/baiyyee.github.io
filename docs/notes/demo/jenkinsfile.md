# Jenkinsfile

```
pipeline {
    agent any
    stages {
        stage("verify tooling"){
            steps (
                sh '''
                    docker version
                    docker info
                    docker-compose -v
                    curl --version
                '''
            )
        }

        stage("Prune Docker Data"){
            steps {
                sh "docker system prune -a --volumes --wait"
            }
        }

        stage("Start Container"){
            steps {
                sh "docker-compose up -d --no-color --wait"
                sh "docker-compose ps"
            }
        }

        stage("Run tests against the container"){
            steps {
                sh "curl http://localhost:3000/param?query=demo | jq"
            }
        }
    }

    post {
        always {
            sh "docker-compose down --remove-orphans -v"
            sh "docker-compose ps"
        }
    }
}
```