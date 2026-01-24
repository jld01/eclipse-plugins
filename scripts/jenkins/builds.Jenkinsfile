pipeline {
    agent {
        kubernetes {
            label 'migration'
        }
    }
    tools {
        // https://github.com/eclipse-cbi/jiro/wiki/Tools-(JDK,-Maven,-Ant)#apache-maven
        // https://eclipse.dev/cbi/jiro/Tools/#apache-maven
        maven 'apache-maven-latest'

        // https://github.com/eclipse-cbi/jiro/wiki/Tools-(JDK,-Maven,-Ant)#eclipse-temurin
        // https://eclipse.dev/cbi/jiro/Tools/#eclipse-temurin
        jdk 'temurin-jdk21-latest'
    }

    stages {
        stage('Build') {
            steps {

                sh "mvn \
                        --batch-mode \
                        --show-version \
                        clean verify \
                        -P production \
                        -Dmaven.repo.local=/home/jenkins/.m2/repository \
                        --settings /home/jenkins/.m2/settings.xml \
                "
            }
        }
        stage('Upload') {
            steps {
                sshagent ( ['projects-storage.eclipse.org-bot-ssh']) {
                   sh './scripts/jenkins/builds-upload.sh'
                }
            }
        }
    }
    post {
        success {
            // if/when tests are added, the results can be collected by uncommenting the next line
            // junit '*/*/target/surefire-reports/*.xml'
            archiveArtifacts 'repositories/org.eclipse.embedcdt-repository/target/org.eclipse.embedcdt.repository-*.zip'
        }
    }
}
