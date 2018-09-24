pipeline {
    agent any

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Compile') {
            steps {
                gradlew('clean', 'classes')
            }
        }
        stage('Unit Tests') {
            steps {
                gradlew('test')
            }
        }
        stage('Assemble') {
            steps {
                gradlew('assemble')
                stash includes: '**/build/libs/*.jar', name: 'app'
            }
        }
        stage('Push Image') {
            environment {
                DOCKER_USERNAME = credentials('DOCKER_USERNAME')
                DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
                DOCKER_EMAIL = credentials('DOCKER_EMAIL')
            }
            steps {
                gradlew('dockerPushImage')
            }
        }
    }
    post {
        failure {
            mail to: 'benjamin.muschko@gmail.com', subject: 'Build failed', body: 'Please fix!'
        }
    }
}

def gradlew(String... args) {
    sh "./gradlew ${args.join(' ')} -s"
}