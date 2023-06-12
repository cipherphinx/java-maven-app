pipeline {
    agent any
    tools {
        maven 'maven-3.8.8'
    }

    stages {

            stage("test") {
                steps {
                    script {
                       echo "Testing the application..."
                       echo "Executing the pipeline for branch $BRANCH_NAME"
                    }
                }
            }

            stage("build") {
                steps {
                    script {
                         echo "Building the application..."
                    }

                }

            }

            stage("deploy") {
                steps {
                    script {
                        def dockerCmd = 'docker run -p 3080:3080 -d cipherphinx/demo-app:jma-1.0'
                        sshagent(['ec2-server-key']) {
                            sh "ssh -o StrictHostKeyChecking=no ec2-user@13.229.209.189 ${dockerCmd}"
                        }
                    }
                }
            }
        }
}