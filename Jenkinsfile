pipeline {
    agent any

    environment {
        DROPBOX_TOKEN = credentials('DROPBOX_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/joackagui/SmartBoard.git'
            }
        }

        stage('Build APK') {
            steps {
                sh './gradlew assembleDebug'
            }
        }

        stage('Upload to Dropbox') {
            steps {
                script {
                    def apkPath = "app/build/outputs/apk/debug/app-debug.apk"
                    def dropboxDest = "/SmartBoard/app-debug.apk"

                    sh """
                    if [ ! -z "$DROPBOX_TOKEN" ]; then
                        curl -X POST https://content.dropboxapi.com/2/files/upload \\
                            --header "Authorization: Bearer $DROPBOX_TOKEN" \\
                            --header "Dropbox-API-Arg: {\\"path\\": \\"${dropboxDest}\\",\\"mode\\":\\"overwrite\\"}" \\
                            --header "Content-Type: application/octet-stream" \\
                            --data-binary @${apkPath}
                    else
                        echo "No DROPBOX_TOKEN found. Skipping upload."
                    fi
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed.'
        }
        failure {
            echo 'Something went wrong.'
        }
    }
}