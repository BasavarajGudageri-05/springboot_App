pipeline{
    agent any

    tools{
        jdk 'java-17'
        maven 'maven'
    }

    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/BasavarajGudageri-05/springboot_App.git'
            }
        }

        stage('compile'){
            steps{
                sh 'mvn compile'
            }
        }

        stage('build'){
            steps{
                sh 'mvn clean package'
            }
        }

        stage('code quality check'){
            steps{
                sh '''
              mvn sonar:sonar \
             -Dsonar.projectKey=Java \
             -Dsonar.host.url=http://54.234.155.171:9000 \
             -Dsonar.login=96776e3154522156b6677b45d29b76caf63f347b
                '''
            }
        }

        stage('Docker image Build'){
            steps{
                sh 'docker build -t babugudageri/twitter-app:${GIT_COMMIT} .'
            }
        }

        stage('Docker push'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh 'docker push babugudageri/twitter-app:${GIT_COMMIT}'
                }
            }
        }

        stage('k8s Deployment'){
            steps{
                withKubeConfig(credentialsId: 'kubecredID') {
                    sh '''
                    kubectl apply -f Deployment.yml
                    kubectl apply -f service.yml
                    '''
                }
            }
        }
    }

    post {
        success {
            emailext (
                subject: "✅ SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hi Team,

Good news 🎉

Pipeline completed successfully.

Job Name   : ${JOB_NAME}
Build No   : ${BUILD_NUMBER}
Status     : SUCCESS
Build URL  : ${BUILD_URL}

Regards,
Jenkins
""",
                to: "basavarajgudageri07@gmail.com"
            )
        }

        failure {
            emailext (
                subject: "❌ FAILURE: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hi Team,

Pipeline has FAILED ❌

Job Name   : ${JOB_NAME}
Build No   : ${BUILD_NUMBER}
Status     : FAILURE
Build URL  : ${BUILD_URL}

Please check logs for details.

Regards,
Jenkins
""",
                to: "basavarajgudageri07@gmail.com"
            )
        }
    }
}

