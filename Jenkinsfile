pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-packages-cred-jenkins-pipeline')

        JAVA_HOME  = tool name: 'jdk21'
        MAVEN_HOME = tool name: 'maven3'

        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${PATH}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Java & Maven') {
            steps {
                sh '''
                    echo "===== Java Version ====="
                    java -version

                    echo "===== Maven Version ====="
                    mvn -version
                '''
            }
        }

        stage('Build') {
            steps {
                configFileProvider([
                    configFile(
                        fileId: 'maven-github-settings',
                        variable: 'MAVEN_SETTINGS'
                    )
                ]) {
                    sh '''
                        mvn -s "$MAVEN_SETTINGS" -B clean package
                    '''
                }
            }
        }

        stage('Deploy to GitHub Packages') {
            steps {
                configFileProvider([
                    configFile(
                        fileId: 'maven-github-settings',
                        variable: 'MAVEN_SETTINGS'
                    )
                ]) {
                    sh '''
                        export GH_USER="$GITHUB_CREDS_USR"
                        export GH_TOKEN="$GITHUB_CREDS_PSW"

                        mvn -s "$MAVEN_SETTINGS" -B deploy
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build and deployment to GitHub Packages completed successfully."
        }

        failure {
            echo "❌ Pipeline failed. Check the console output for details."
        }

        always {
            echo "🏁 Pipeline execution completed."
        }
    }
}
