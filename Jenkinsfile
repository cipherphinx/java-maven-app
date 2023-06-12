#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
     remote: 'https://github.com/cipherphinx/jenkins-shared-library.git',
     credentialsId: 'github-credentials'
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven-3.8.8'
    }

    stages {

            stage('increment version') {
                steps {
                    script {
                         echo "Incrementing app version..."
                         sh 'mvn build-helper:parse-version versions:set \
                            -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                            versions:commit'
                         def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                         def version = matcher[0][1]
                         env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                    }
                }
            }

            stage('build app') {
                steps {
                    script {
                         echo "Building the application jar..."
                         buildJar()
                    }
                }
            }

            stage('build image') {
                steps {
                    script {
                         echo "Building docker image..."
                         buildImage(env.IMAGE_NAME)
                         dockerLogin()
                         dockerPush(env.IMAGE_NAME)
                    }
                }
            }

            stage('deploy') {
                steps {
                    script {
                        echo 'deploying docker image to EC2'
                        // def dockerCmd = "docker run -p 8080:8080 -d ${IMAGE_NAME}"
                        // def dockerComposeCmd = "docker compose -f docker-compose.yaml up --detach"
                        def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
                        def ec2Instance = "ec2-user@13.229.209.189"

                        sshagent(['ec2-server-key']) {
                            sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
                            sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                            sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                        }
                    }
                }
            }

            stage('commit version update') {
                steps {
                    script {
                        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                            //sh 'git config --global user.email "jenkins@gmail.com"'
                            //sh 'git config --global user.name "jenkins"'
                            sh "git remote set-url origin https://cipherphinx:${GITHUB_TOKEN}@github.com/cipherphinx/java-maven-app.git"
                            //sh "git remote set-url origin https://${GITHUB_TOKEN}@github.com:cipherphinx/java-maven-app.git"
                            sh 'git add .'
                            sh 'git commit -m "ci: version bump"'
                            sh 'git push origin HEAD:jenkins-jobs'
                        }
                    }
                }
            }
    }
}