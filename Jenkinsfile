pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git branch: 'main', 
                credentialsId: 'docker-cred', 
                url: 'https://github.com/Thakur156/cicd-end-to-end'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t thakur156/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push thakur156/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git branch: 'main', 
                credentialsId: 'docker-cred', 
                url: 'https://github.com/Thakur156/cicd-end-to-end/deploy'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            environment {
            GIT_REPO_NAME = "cicd-end-to-end"
            GIT_USER_NAME = "thakur156"
        }
            steps {
                script{withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                        sed -i "s/replaceimagetag/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
