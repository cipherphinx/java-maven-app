#!/usr/bin/env groovy

pipeline {
    agent none

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
            when {
                expression {
                    BRANCH_NAME == 'main'
                }
            }
            steps {
                script {
                     echo "Building the application..."
                }

            }

        }

        stage("deploy") {
            when {
                expression {
                    BRANCH_NAME == 'main'
                }
            }
            steps {
                script {
                    echo "Deploying the application..."
                }
            }
        }
    }
}