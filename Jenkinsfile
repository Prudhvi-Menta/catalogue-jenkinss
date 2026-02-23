pipeline {
    // This is pre-build section
    agent {
        node {
            label 'jenkins-agent'
        }
    }
    environment{
        Course="Jenkins"
        appVersion = ""
    }
    options{
        timeout(time: 10, unit: 'SECONDS')
        disableConcurrentBuilds()
    }

    // This is build
    stages {
        stage('Read Version') {
            steps {
                script{
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "appversion is : $appVersion"
                }
            }
        }

        stage('Test') {
            steps {
                script{
                    sh """
                        echo "This is Test stage"
                        echo $Course
                    """
                }
            }
        }
        stage('Deploy') {
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "alice,bob"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
            // }
            
            when{
                expression { "$params.DEPLOY" == "true" }
            }
            steps {
                 script{
                    sh """
                        echo "This is Deploy stage"
                        echo $Course
                    """
                }
            }
        }
    }

    post{
        always{
            echo "I will execute in post section."
            cleanWs()
        }
        success{
            echo "I will execute if pipeline succeded."
        }
        failure{
            echo "I will execute if pipeline failed."
        }
        aborted{
            echo "This pipeline is aborted"
            echo "Job name is $JOB_BASE_NAME, Build id is $BUILD_NUMBER has failed against this git commit $GIT_COMMIT"
        }
    }
}