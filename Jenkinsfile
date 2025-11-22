pipeline {
    agent { label "Jenkins-Agent" }
    environment {
              APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'work', credentialsId: 'github', url: 'https://github.com/riteshsute/gitops-register-app'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
    steps {
        script {

            sh """
               git config --global user.name "riteshsute"
               git config --global user.email "suteritesh@gmail.com"
               git pull origin work --rebase
               git add deployment.yaml
               git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
            """

            withCredentials([usernamePassword(
                credentialsId: 'github',
                usernameVariable: 'GIT_USER',
                passwordVariable: 'GIT_PASS'
            )]) {
                sh """
                    git push https://${GIT_USER}:${GIT_PASS}@github.com/riteshsute/gitops-register-app.git work
                """
            }
        }
    }
}

      
    }
}
