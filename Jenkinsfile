pipeline {
    agent any

    stages {
        stage('Validate') {
            steps {
                
                sh "mvn validate"

                sh "mvn clean"

            }
        }
// 1.Compile the code (This should create an artifact)
         stage('Build') {
            steps {
                
                sh "mvn compile"

            }
        }
//2.Run unit test
        stage('Test') {
            steps {
                
                sh "mvn test"

            }
            // 3.Publish the report in Junit format
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml' //it is from the syntax genertor snd ** -> means anything in the Start
                }
            }
        }

        stage('Package') {
            steps {
                
                sh "mvn package"

            }

            post {
                success { //when the step is successful archive the artfact
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false

                   
                }
            }
        }
    }
}