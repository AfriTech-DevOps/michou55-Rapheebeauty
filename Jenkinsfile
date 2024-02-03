pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('2ff349e1-7010-4200-99a8-ac17d7c56046')
    }

    stages {
        stage('Clean Workspace'){
            steps {
                cleanWs()
            }
        }
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/dev'], [name: '*/qa'], [name: '*/prod']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AfriTech-DevOps/michou55-Rapheebeauty']])
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=mimi -Dsonar.projectKey=mimi"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage ('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage ('Docker Build') {
            steps {
                  sh 'docker build -t michraphee/webapp1:latest .'
            }
        }
        stage ('Trivy Image Scan') {
            steps {
                sh 'docker run --rm aquasec/trivy:0.18.3 mimizok/michraphee:latest'
            }
        }
        stage ('Dcoker Push') {
            steps {
                sh 'docker push mimizok/michraphee:latest'
            }
        }
    }

    // stage('Deploy') {
    //     steps {
    //         script {
    //             dir('./k8s') {
    //                 kubeconfig(credentialsId: '')
    //             }
    //         }
    //     }
    // }
}

