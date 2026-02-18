pipeline {
    agent any

    tools {
        // Make sure you have configured a Maven tool named 'Maven 3' in Global Tool Configuration
        maven 'Maven 3' 
        // jdk 'JDK 17' // Uncomment and configure if your agent doesn't have default Java 17
    }

    environment {
        // REPLACE THESE VARIABLES WITH YOUR ACTUAL VALUES
        EC2_HOST = '3.15.123.456'     // The Public IP of your EC2 instance
        EC2_USER = 'ec2-user'         // User (e.g., ec2-user, ubuntu)
        TARGET_DIR = '/usr/local/tomcat/webapps' // Tomcat webapps directory on EC2
        
        // The ID of the SSH private key credential stored in Jenkins
        SSH_CRED_ID = 'ec2-ssh-key' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                // Skip tests for speed in demo, remove -DskipTests for production
                sh 'mvn clean package -DskipTests' 
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying to EC2...'
                sshagent(credentials: ["${SSH_CRED_ID}"]) {
                    // 1. Remove old app (optional, depends on deployment strategy)
                    // sh "ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'rm -rf ${TARGET_DIR}/springboot-hello*'"

                    // 2. secure copy the new WAR file
                    // We rename it to ROOT.war to serve it at the root context, or keep original name
                    sh "scp -o StrictHostKeyChecking=no target/*.war ${EC2_USER}@${EC2_HOST}:${TARGET_DIR}/springboot-hello.war"
                    
                    // 3. Optional: Restart Tomcat if hot-deployment isn't enough
                    // sh "ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'sudo systemctl restart tomcat'"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
