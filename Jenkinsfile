pipeline {
    agent any

    environment {
        JAR_DIR = "/opt/hello-world"
        JAR_NAME = "hello-world-0.0.1-SNAPSHOT.jar"
        LOG_FILE = "app.log"
    }

    stages {
        stage('Restart Spring Boot App') {
            steps {
                script {
                    sh """
                        echo "Stopping old instance (if any)..."
                        pkill -f \$JAR_NAME || true

                        echo "Starting new instance..."
                        cd \$JAR_DIR
                        java -jar \$JAR_NAME
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ App deployed and restarted successfully!"
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}