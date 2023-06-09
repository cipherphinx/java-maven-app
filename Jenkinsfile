pipeline {
    agent any
    parameters {
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTest', defaultValue: true, description: '')
    }

    stages {

        stage("build") {
            steps {
                echo 'building the application...'
            }
        }

        stage("test") {
            when {
                expression {
                    params.executeTest == true
                }
            }
            steps {
                echo 'testing the application...'
            }

        }

        stage("deploy") {
            steps {
                echo 'deploying the application...'
                echo "deploying version  ${params.VERSION}"
            }
        }
    }
}