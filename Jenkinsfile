pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3.9"
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/Mouradelbourakadi/golden-war.git'
            }
        }

        stage('Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage('Deploy to AWS') {
            steps {
                script {
                    // Copy the WAR file to the remote AWS instance using SCP
                    def remoteWarPath = "/appli/*.war"
                    sh "scp -i omega_frontend_key.pem golden/target/*.war ec2-user@15.237.3.23:${remoteWarPath}"
                }
            }
        }

        stage('Start Tomcat Container') {
            steps {
                script {
                    // Start the Tomcat Docker container on the AWS instance
                    def containerCommand = "docker run -d -p 8080:8080 -v /appli/*.war:/usr/local/tomcat/webapps/ tomcat:tomcat"
                    sh "ssh ec2-user@15.237.3.23 '${containerCommand}'"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Add steps to verify the deployment, e.g., running tests or checking application accessibility
                    sh "ssh ec2-user@$15.237.3.23 'ls -l /appli'"
                }
            }
        }
    }

    post {
        success {
            echo 'Déploiement réussi!'
        }
        failure {
            echo 'Échec du déploiement. Vérifiez les logs pour plus d\'informations.'
        }
    }
}
