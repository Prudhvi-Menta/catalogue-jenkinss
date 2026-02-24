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
        ACC_ID="450558841952"
        PROJECT="roboshop"
        COMPONENT="catalogue"
    }
    // options{
    //     timeout(time: 10, unit: 'SECONDS')
    //     disableConcurrentBuilds()
    // }

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

        stage('Install dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }

        stage('build image') {
            steps {
                script{
                    
                        withAWS(region:'eu-central-1',credentials:'aws creds') {
                        sh """
                        # aws login
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                        # docker build
                        docker build ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/$PROJECT/$COMPONENT:$appVersion
                        # docker push
                        docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/$PROJECT/$COMPONENT:$appVersion
                        """
                }
            }
        }

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