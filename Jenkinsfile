pipeline {
    agent any

    tools {
        // Installer la version de Maven configurée comme "M3" et l'ajouter au chemin.
        maven "maven3.9"
    }
    environment {
        SSH_KEY = credentials('omega_frontend_key.pem')
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
                    sshagent(credentials: ['omega_frontend_key.pem']) {
                        sh """
                            scp -i \${SSH_KEY} golden/target/*.war ec2-user@15.237.3.23:/appli/
                        """
                    }
                }
            }
        }

        stage('Start Tomcat Container') {
            steps {
                script {
                    // Démarrer le conteneur Docker Tomcat sur l'instance AWS
                    def containerCommand = "docker run -d -p 8080:8080 -v /appli/*.war:/usr/local/tomcat/webapps/ tomcat:tomcat"
                    sh "ssh ec2-user@15.237.3.23 '${containerCommand}'"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Ajouter des étapes pour vérifier le déploiement, par exemple, exécuter des tests ou vérifier l'accessibilité de l'application
                    sh "ssh ec2-user@15.237.3.23 'ls -l /appli'"
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
