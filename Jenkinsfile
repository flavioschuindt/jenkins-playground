#!groovy​
pipeline {
    agent any

    stages {
        stage('Clone repository') {
            /* Let's make sure we have the repository cloned to our workspace */
            steps{
                checkout scm
            }
        }

        stage('Build image') {
            /* This builds the actual image; synonymous to
             * docker build on the command line */
            steps{
                script{
                    app = docker.build("jenkins-playground/hellonode")
                }
            } 
        }

        stage('Test image') {
            /* Ideally, we would run a test framework against our image.
             * For this example, we're using a Volkswagen-type approach ;-) */
            steps {
                script {
                    app.inside {
                        sh 'echo "Tests passed"'
                    }
                }
            }  
        }

        stage('Push image') {
            /* Finally, we'll push the image with two tags:
             * First, the incremental build number from Jenkins
             * Second, the 'latest' tag.
             * Pushing multiple tags is cheap, as all the layers are reused. */
             steps {
                script {
                    docker.withRegistry('http://local.registry:5000') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    } 
                }
             }      
        }
    }

    post {
        always {
            script {
                docker.image('jenkins-playground/hellonode:latest').withRun('-p 8000:8000') {
                    sh 'echo "App running!"'
                }
            } 
        }
    }
}