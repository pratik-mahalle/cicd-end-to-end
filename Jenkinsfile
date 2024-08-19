pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '10ecc777-30fc-4216-af4c-6442565ae033'
                url: 'https://github.com/pratik-mahalle/cicd-end-to-end',, 
                branch: 'main'
           }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker Image'
                    sh '''
                    docker build -t pratikmahalle17/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: '390c3b02-70dc-48b8-93dc-fdefe4ea29a3', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo 'Logging in to Docker Hub'
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        echo 'Pushing Docker Image'
                        docker push pratikmahalle17/cicd-e2e:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }

        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '10ecc777-30fc-4216-af4c-6442565ae033', 
                url: 'https://github.com/pratik-mahalle/cicd-end-to-end.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '10ecc777-30fc-4216-af4c-6442565ae033', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/pratik-mahalle/cicd-end-to-end.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
