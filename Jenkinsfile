pipeline{
    agent any
    
    environment{
        SONAR_HOME = tool "sonar"
    }
    
    stages{
        stage("Clone Code from Github"){
            steps{
                git url: "https://github.com/ROHITSINGHB/wanderlust.git", branch: "devops"
            }
        }
        
        stage("SonarQube Quality Analysis"){
            steps{
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv("sonar"){
                        bat "\"${SONAR_HOME}\\bin\\sonar-scanner.bat\" -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust -Dsonar.token=%SONAR_TOKEN%"
                    }
                }
            }
        }
        stage("OWASP Dependecy Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan"){
            steps{
                bat "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage("Deploy using Docker compse"){
            steps{
                bat "docker-compose up -d"
            }
        }
        
    }
    
    post {
        always {
            echo "Pipeline execution completed"
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed! Check logs for details."
        }
    }
}
