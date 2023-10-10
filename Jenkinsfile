pipeline {
    agent any

    tools {
        // Installer la version de Maven configurée comme "M3" et l'ajouter au chemin.
        maven "maven3.9"
    }
    
    environment {
        REMOTE_HOST = '15.237.3.23'
        REMOTE_PATH = '/appli/'
    }
    
    stages {
        stage('Clone') {
            steps {
                // Cloner le référentiel Git
                git 'https://github.com/Mouradelbourakadi/golden-war.git'
            }
        }

        stage('Build') {
            steps {
                // Construire le projet avec Maven
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        
        stage('Connection copy deploy') {
            steps {
                script {
                    // Utilisation de sshagent pour gérer les clés SSH
                    sshagent(credentials: ['omeg_frontend_key']) {
                        // Copier le fichier WAR sur le serveur distant
                        sh "scp golden/target/golden.war ec2-user@${REMOTE_HOST}:${REMOTE_PATH}"
                        // Exécuter le conteneur Docker sur le serveur distant
                        //sh "ssh ec2-user@${REMOTE_HOST} 'docker stop tomcat-container'"
                        sh "ssh ec2-user@${REMOTE_HOST} 'docker run -d -p 8080:8080 --name tomcat-container -v ${REMOTE_PATH}/golden.war:/usr/local/tomcat/webapps/ tomcat:8'"
                    }
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
