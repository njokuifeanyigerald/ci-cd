pipeline{
    agent any
	tools {
        maven 'maven-3.6.3'
        jdk 'jdk8'
    }
    stages{
        stage('init'){
            steps{
                script{
                    println("Hello world")
                }
            }
        }
        stage('Build') {
            steps {
		script{
		    sh 'rm -rf maven_project/target'
	            dir('maven_project'){
			sh 'mvn -B -DskipTests clean package' 
		    }
		}
            }
        }
        stage('Test') {
            steps {
		script{
	            dir('maven_project'){
			sh 'mvn test' 
		    }
		}
            }
        }
        stage('upload jar to AWS'){
            steps{
                script{                    
                    withAWS(credentials: 'my-cba-aws-credential', region: 'eu-west-2') {
                        sh '''echo "Uploading the tested jar file to s3 for later deployments" '''
                        s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'maven_project/target/my-app-1.0-SNAPSHOT.jar', bucket:'document-gerald', path:'ci-demo/javaapp/myapp.jar')
                    }
                }
            }
        }
    }
   post{
        always{
                script{  
                    echo  '''this is always executed '''
		    cleanWs()
            }
        }
    }
}
