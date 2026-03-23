pipeline {
    agent any

    environment {
        EC2_USER = "ubuntu"
        EC2_HOST = "<EC2-PUBLIC-IP>"
        APP_DIR = "/home/ubuntu/app"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/<your-username>/<repo-name>.git'
            }
        }

        stage('Build App') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_HOST << EOF
                        rm -rf $APP_DIR
                        mkdir -p $APP_DIR
                        exit
                    EOF
                    """

                    sh """
                    scp -r * $EC2_USER@$EC2_HOST:$APP_DIR
                    """

                    sh """
                    ssh $EC2_USER@$EC2_HOST << EOF
                        cd $APP_DIR
                        npm install
                        pm2 delete app || true
                        pm2 start index.js --name app
                    EOF
                    """
                }
            }
        }
    }
}