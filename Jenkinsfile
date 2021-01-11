def agentLabel
if (BRANCH_NAME == 'main') {
    agentLabel = 'master'
} else {
    agentLabel = ''
}

pipeline {
    agent { label agentLabel }

    stages {
        stage('Pre-build') {
            steps {
                echo "NODE_NAME = ${env.NODE_NAME}"
                echo 'Pre-build..'
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
                sh './gradlew test'
            }
        }
        stage('Sonarqube') {
            environment {
                scannerHome = tool 'SonarQubeScanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh './gradlew sonarqube'
                }
                timeout(time: 10, unit: 'MINUTES') {
                    // Needs to be changed to true in the real project.
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying....'
                sh 'rm -rf /srv/Backend/orderbook/*'
                sh 'cd ./build && mv jib-image.tar /srv/Backend/cd/orderbook/orderbook.tar'
                sh 'touch /srv/Backend/cd/orderbook/deploy'
            }
        }
        stage('Release') {
            when { allOf { branch 'dev'; triggeredBy 'UserIdCause' } }
            steps {
                sshagent (credentials: ['jenkins']) {
                    echo 'Pushing dev to main'
                    sh 'git push git@github.com:IT-REX-Platform/Orderbook.git dev:main'
                }
            }
        }
    }
}
