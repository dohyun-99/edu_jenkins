properties([pipelineTriggers([githubPush()])])

pipeline {
    environment {
        // Global 변수 선언
        // Harbor 사용시 : 앞에 project 이름이 붙는다. 예) edu
        dockerRepo = "dodom2/edu_jenkins"
        // nexus
        //dockerRepo = "edu1"
        dockerCredentials = 'docker_ci_test'
        dockerImageVersioned = ""
        dockerImageLatest = ""
    }

    agent any

    stages {
        /* checkout repo */
        stage('Checkout SCM') {
            steps{
                script{
                    checkout scm
                 }
            }   
        }
        
        stage("Building docker image"){
            steps{
                script{
                    dockerImageVersioned = docker.build dockerRepo //+ ":$BUILD_NUMBER"
                    dockerImageLatest = docker.build dockerRepo + ":latest"
                }
            }
        }
        stage("Pushing image to registry"){
            steps{
                script{
                    // if you want to use custom registry, use the first argument, which is blank in this case
                    // Harbor
                    //docker.withRegistry('https://211.252.85.148:40002', dockerCredentials){
                    // nexus
                    docker.withRegistry( 'http://211.43.12.178:40010', dockerCredentials){
                        dockerImageVersioned.push()
                        dockerImageLatest.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $dockerRepo"//:$BUILD_NUMBER"
            }
        }
    }

    /* Cleanup workspace */
    post {
       always {
           deleteDir()
       }
   }
}
