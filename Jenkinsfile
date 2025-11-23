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
                        git config --global user.email "suteritesh@gmail.com"
                        git config --global user.name "riteshsute"
        
                        # stash local changes so pull won’t fail
                        git stash
        
                        # pull latest branch changes
                        git pull origin work --rebase
        
                        # apply stashed deployment.yaml changes
                        git stash pop || true
        
                        git add deployment.yaml
                        git commit -m "Updated Deployment Manifest" || true
                    """
        
                    withCredentials([usernamePassword(
                        credentialsId: 'github',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_PASS'
                    )]) {
        
                        sh """
                            git remote set-url origin https://${GIT_USER}:${GIT_PASS}@github.com/riteshsute/gitops-register-app.git
                            git push origin HEAD:work
                        """
                    }
                }
            }
        }



      
    }
}
