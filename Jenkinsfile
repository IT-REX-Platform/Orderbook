pipeline {
    agent any

    stages {
        stage('Pre-build') {
            steps {
                echo "NODE_NAME = ${env.NODE_NAME}"
                echo 'Pre-build..'
                sh './gradlew npmInstall'
            }
        }
        stage('Build') {
            steps {
                echo 'Building Orderbook..'
                sh './gradlew -Pprod bootJar jibBuildTar'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying....'
                sh 'rm -rf /srv/Backend/orderbook/*'
                sh 'cd ./build && mv jib-image.tar /srv/Backend/orderbook/orderbook.tar'
                sh 'touch /srv/Backend/orderbook/deploy'
            }
        }
    }
}
