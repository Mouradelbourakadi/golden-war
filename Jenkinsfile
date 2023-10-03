pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3.9"
    }

   stages {
        stage('clone') {
        steps {
        git 'https://github.com/goldentechnologyrec/golden-war.git'
        }
    }
    stage('build') {
        steps {
             sh "mvn -Dmaven.test.failure.ignore=true clean package"
        }
    }
  // stage('deploy') {
  //      steps {
  //          sh "mkdir -p /appli/archiveArtifacts"
  //          sh "cp golden/target/*.war /appli/archiveArtifacts"
  //      }
  //  }
}
}
