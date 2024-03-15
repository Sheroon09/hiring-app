pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Docker Build') {
            steps {
                sh "docker build . -t sheroon/hiring:$BUILD_NUMBER"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'sheroon', variable: 'hubPwd')]) {
                    sh "docker login -u sheroon -p Sheroon@09"
                    sh "docker push sheroon/hiring:$BUILD_NUMBER"
                }
            }
        }
        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Sheroon09/Hiring-app-argocd1.git'
            }
        } 
        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'githubtoken', variable: 'GIT_TOKEN')]) { 
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/14/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GIT_TOKEN@github.com/Sheroon09/Hiring-app-argocd.git main
                        '''                        
                    }
                }
            }   
        }
    }
} 
