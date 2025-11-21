pipeline {
    agent any

    environment {
        MAVEN_SETTINGS = "${WORKSPACE}/maven/settings.xml" // settings.xml from repo
        SONAR_TOKEN = credentials('sonar-token') // Jenkins stored Sonar token
        EMAIL_RECIPIENTS = 'your_email@example.com'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/subahan214/MyWebApp11.git', branch: 'main'
            }
        }

        stage('Build & Test') {
            steps {
                sh "mvn clean test jacoco:report -s ${MAVEN_SETTINGS}"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=MyWebApp11 \
                        -Dsonar.host.url=http://34.228.36.53:9000/ \
                        -Dsonar.login=${SONAR_TOKEN} \
                        -Dsonar.java.coveragePlugin=jacoco \
                        -s ${MAVEN_SETTINGS}
                """
            }
        }
    }

    post {
        success {
            emailext (
                subject: "Build SUCCESS: ${PROJECT_NAME} #${BUILD_NUMBER}",
                body: """
                    Build URL: ${BUILD_URL}
                    Status: ${BUILD_STATUS}
                    Changes: ${CHANGES}
                    Failed Tests: ${FAILED_TESTS}
                """,
                to: "${EMAIL_RECIPIENTS}"
            )
        }
        failure {
            emailext (
                subject: "Build FAILURE: ${PROJECT_NAME} #${BUILD_NUMBER}",
                body: """
                    Build URL: ${BUILD_URL}
                    Status: ${BUILD_STATUS}
                    Cause: ${CAUSE}
                    Failed Tests: ${FAILED_TESTS}
                """,
                to: "${EMAIL_RECIPIENTS}"
            )
        }
    }
}
