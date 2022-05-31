pipeline {
    agent {
        node {
            label 'demo2022'
        }
    }
    environment {
        IMAGE_VER_TAG ="${env.BUILD_NUMBER}"
        myImageName = "demotrain2022"
        myDockerhub    = "sarwantr"
    }
    stages{
        stage('SCM Checkout'){
            steps{
                git branch:'dev' credentialsId: 'santr_github', url: 'https://github.com/saronavee/new-web-app.git'
            }
        }
        stage('maven build'){
            steps{
                sh 'mvn clean install'
            }
        }
        stage('Docker build and deploy'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhublogin', passwordVariable: 'DOCKERPASS', usernameVariable: 'DOCKERUSER')]){
                    script {
                        sh '''
                            sudo docker login -u ${DOCKERUSER} -p ${DOCKERPASS} docker.io
                            sudo docker build -t docker.io/${myDockerhub}/${myImageName}:${IMAGE_VER_TAG} .
                            sudo docker push docker.io/${myDockerhub}/${myImageName}:${IMAGE_VER_TAG}
                            sudo docker ps -f name=${myImageName} -q | xargs --no-run-if-empty sudo docker container stop
                            sudo docker container ls -a -fname=${myImageName} -q | xargs -r sudo docker container rm
                            sudo docker system prune -f -a
                            sudo docker run -d -p 8082:8080 --name ${myImageName}_${IMAGE_VER_TAG} ${myDockerhub}/${myImageName}:${IMAGE_VER_TAG}
                            '''
                    }
                }
            }
        }
    }
}
