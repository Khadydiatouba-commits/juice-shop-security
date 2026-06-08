pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Lancer Juice Shop') {
            steps {
                sh '''
                    npm install
                    nohup npm start &
                    sleep 20
                '''
            }
        }

        stage('Scan DAST avec ZAP') {
            steps {
                sh '''
                    zaproxy -cmd \
                        -quickurl http://localhost:3000 \
                        -quickprogress \
                        -quickout zap_rapport.html
                '''
            }
        }

        stage('Archiver le rapport') {
            steps {
                archiveArtifacts artifacts: 'zap_rapport.html', fingerprint: true
            }
        }
    }

    post {
        always {
            emailext (
                subject: "Rapport ZAP - Build #${BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: "Le scan ZAP est termine. Statut : ${currentBuild.currentResult}. Voir : ${BUILD_URL}",
                to: 'khadydiatouba@esp.sn',
                attachmentsPattern: 'zap_rapport.html'
            )
        }
    }
}
