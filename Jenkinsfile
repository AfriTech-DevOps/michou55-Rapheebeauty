pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('2ff349e1-7010-4200-99a8-ac17d7c56046')
        SCANNER_HOME=tool 'sonar-scanner'
        BRANCH_NAME = "${GIT_BRANCH.split("/")[1]}"
    }

    stages {
        stage('Clean Workspace'){
            steps {
                cleanWs()
            }
        }
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/dev'], [name: '*/qa'], [name: '*/prod']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AfriTech-DevOps/michou55-Rapheebeauty.git']])
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                   sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=rapheeproject -Dsonar.projectName=rapheeproject"
                }
            }
        }
        stage('Trivy File Scan'){
            steps{
                script{
                    sh 'docker run --rm aquasec/trivy:0.18.3 fs . > trivy_result.txt'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Secret text'
                }
            }
        }
        stage ('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
          stage('Docker Build'){
            steps{
                script{
                   def imageTag = determineTargetEnvironment()
                   sh "docker build -t mimizok/michraphee:${imageTag} ."
                   echo "Image Build Successfully"
                }
            }
        }
         stage('Trivy Image Scan'){
            steps{
                script{
                    def imageTag = determineTargetEnvironment()
                    sh "trivy image mimizok/michraphee:${imageTag}"
                }
            }
        }
        stage('Docker push'){
            steps{
                script{
                    def imageTag = determineTargetEnvironment()
                    sh "docker push mimizok/michraphee:${imageTag}"
                    echo "Push Image to Registry"
                }
            }
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


