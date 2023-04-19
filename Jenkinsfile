pipeline {
    agent { label 'master' }
    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
        GIT_COMMIT_HASH = sh(
                script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
                returnStdout: true)
    }
    stages {
        stage('image building') {
            steps {
               sh '''#!/bin/bash
               sudo docker build -t kavinjaveriya/explorexdemo:${GIT_COMMIT_HASH} .
               '''
            }
        }
        stage('Image Push') {
            steps {
                // Image pushing to docker hub
                sh('''#!/bin/bash
                echo latest version of image is ${GIT_COMMIT_HASH} 
                echo $DOCKERHUB_CREDENTIALS_PSW |sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                sudo docker push kavinjaveriya/explorexdemo:${GIT_COMMIT_HASH}''')
            }
        }
        stage('changing docker image new version') {
            steps {
               withCredentials([gitUsernamePassword(credentialsId: '57e2ff89-c79d-4224-afcc-8797ccf7888d')]) {
                        sh "git clone https://github.com/kavinjeveriya/explorexhelm.git"
                        sh'''
                         cd explorexhelm
                         git checkout master
                         git config --global user.email 'kavinjaveriya@gmail.com'
                         git config --global user.name 'kavinjaveriya'
                         sed -i "s/tag:.*/tag: ${GIT_COMMIT_HASH}/" ./demoapp/values.yaml  
                         git add ./demoapp/values.yaml
                         git commit -m "docker image verison ${GIT_COMMIT_HASH}"
                         git push
                         cd ${WORKSPACE}
                         sudo rm -rf *'''
                }
            }
        }
    }
}