pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                    echo "upload Lambda function"
                    ./upload-lambda-xml.sh
                '''
            }
        }
        stage('Test') {
            steps {
                sh 'echo "run integration test"'
                sh '''
                    pwd
                    whoami
                    which git
                    git --version
                    source ~/.aws/env_var
                    # echo ${AWS_S3_BUCKET}
                    # echo ${AWS_PG_DB_HOST}
                    echo "activate py3"
                    source ~/py36/bin/activate
                    which python3
                    which pytest
                    echo "run pytest"
                    cd src/lambda_xml
                    pytest -q test_lambda_function_xml.py
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo "Build Docker image and Deploy"'
                sh '''
                    which docker
                    $(aws ecr get-login --no-include-email --region us-east-1)
                    docker build -t code2cloud .
                    docker tag code2cloud:latest 629309645488.dkr.ecr.us-east-1.amazonaws.com/code2cloud:latest
                    # docker push 629309645488.dkr.ecr.us-east-1.amazonaws.com/code2cloud:latest
                '''
            }
        }
    }
    post {
        success {
            echo 'This build is successful'
            mail body: "<b>Build OK</b><br>: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> build URL : ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "OK CI: Project name -> ${env.JOB_NAME}", to: "wen.gong@oracle.com";
        }
        failure {
            echo 'This build failed, alert by email ...'
            mail body: "<b>Build Failed</b><br>: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> build URL : ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "wen.gong@oracle.com";
        }
    }
}

