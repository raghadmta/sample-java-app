pipeline {
    agent any
    environment { //variables

        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')

        AWS_S3_BUCKET = "artefact-bucket-repo"
        ARTIFACT_NAME = "hello-world.war"
        // AWS_EB_APP_NAME = "java-webapp"
        // AWS_EB_APP_VERSION = "${BUILD_ID}"
        // AWS_EB_ENVIRONMENT = "Javawebapp-env"

        // SONAR_IP = "54.226.50.200"
        // SONAR_TOKEN = "sqp_aa3cba40e3342d9cff9044e498766a66cf8cc0cc"

    }
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
        //5. Publish the artifacts
        stage('Publish artefacts to S3 Bucket') {
            steps {

                sh "aws configure set region us-east-1"

                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
                
            }
        }

         stage('Deploy') {
            steps {

                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'

                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            
                
            }
        }
        
    }
}