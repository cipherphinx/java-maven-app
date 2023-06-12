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
                     echo "Building the application..."
                     sh 'mvn clean package'
                }
            }
        }

        stage('build image') {
            steps {
                script {
                     echo "building the docker image..."
                     withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', passwordVariable: 'PASS', usernameVariable: 'USER' )]) {
                         sh "docker build -t cipherphinx/demo-app:${IMAGE_NAME} ."
                         sh "echo $PASS | docker login -u $USER --password-stdin"
                         sh "docker push cipherphinx/demo-app:${IMAGE_NAME}"
                     }
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    echo "Deploying docker image to EC2..."
                }
            }
        }

        stage('commit version update') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "jenkins"'

                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'

                        sh "git remote set-url origin https://${GITHUB_TOKEN}@github.com:cipherphinx/java-maven-app.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                    }
                }
            }
        }
    }
}