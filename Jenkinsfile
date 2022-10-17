// pipeline{
//     agent any
// 	tools {
//         maven 'cba-maven-3.6.3' 
//         jdk 'cba-jdk8'
//     }
//     stages{
//         stage('init'){
//             steps{
//                 script{
//                     println("Hello world")
//                 }
//             }
//         }
//         stage('Build') {
//             steps {
// 		script{
// 		    sh 'rm -rf maven_project/target'
// 	            dir('maven_project'){
// 			sh 'mvn -B -DskipTests clean package' 
// 		    }
// 		}
//             }
//         }
//         stage('Test') {
//             steps {
// 		script{
// 	            dir('maven_project'){
// 			sh 'mvn test' 
// 		    }
// 		}
//             }
//         }
//         stage('upload jar to AWS'){
//             steps{
//                 script{                    
//                     withAWS(credentials: 'my-cba-aws-credential', region: 'eu-west-2') {
//                         sh '''echo "Uploading the tested jar file to s3 for later deployments" '''
//                         s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'maven_project/target/my-app-1.0-SNAPSHOT.jar', bucket:'document-gerald', path:'ci-demo/javaapp/myapp.jar')
//                     }
//                 }
//             }
//         }
//     }
//    post{
//         always{
//                 script{  
//                     echo  '''this is always executed '''
// 		    cleanWs()
//             }
//         }
//     }
// }


pipeline{
    agent any
    parameters {
	  choice choices: ['UAT', 'PERFORMACE', 'PROD'], description: 'Target environment', name: 'TARGET_ENV'
    }
	tools {
        maven 'cba-maven-3.6.3'
        jdk 'cba-jdk8'
    }
    stages{
        stage('init'){
            steps{
                script{
                    cleanWs()
                    println("Preparing job with variables")
                    println("deploying in ${TARGET_ENV}")
                    currentBuild.displayName = "#${BUILD_NUMBER} Started by ${currentBuild.getBuildCauses()[0].userId}"
                    currentBuild.description = """Target Env is: ${TARGET_ENV} """
                }
            }
        }
        stage('download jar from s3') {
            steps{
		script{                   
                    withAWS(credentials: 'my-cba-aws-credential', region: 'eu-west-2') {
                        sh '''echo "Downloading jar from s3 for deployment to ${ENV}" '''
                        s3Download bucket: 'document-gerald', file: 'myproject.jar', path: 'ci-demo/javaapp/myapp.jar'
                    }
		 }
             }
        }
        stage('maven deploy') {
            steps {
		 script{
			if(TARGET_ENV == 'PROD'){
			    input('Do you want to proceed deployment to production?')
			}
			 
	                println("Deploying....\nMaven deploy...." )
	                echo"""mvn deploy:deploy-file -DgroupId=<group-id> \
                          -DartifactId=myapp.jar \
                          -Dversion=<version> \
                          -Dpackaging=<type-of-packaging> \
                          -Dfile=<path-to-file> \
                          -DrepositoryId=<id-to-map-on-server-section-of-settings.xml> \
                          -Durl=<url-of-the-repository-to-deploy>"""
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

