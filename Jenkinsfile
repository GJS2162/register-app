pipeline{
	agent{ label 'dev-server'}
	tools {
	jdk 'Java17'
	maven 'Maven3'
	}

	environment{
		APP_NAME="complete-production-e2e-pipeline"
		RELEASE="1.0.0"
		DOCKER_USER="gaurav2162"
		DOCKER_PASS='dockerhub'
		IMAGE_NAME="${DOCKER_USER}" + "/" + "${APP_NAME}" 
		IMAGE_TAG="${RELEASE}-${BUILD_NUMBER}"
	}
	stages{
		stage("Clean WorkSpace"){
			steps{
				cleanWs()
			}
		}
		stage("Checkout from SCM"){
			steps{
			git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/GJS2162/register-app'
			}
		}
		stage("Build Application"){
			steps{
			sh "mvn clean package" 
			}
		}
		stage("Test Application"){
			steps{
			sh "mvn test" 
			}
		}
		stage("SonarQube Ananlysis"){
			steps{
			  script{
				  withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
				sh "mvn sonar:sonar" 
				}
		}
	}
     }
            stage("Quality Gate"){
		steps{
			script{
				  waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'  
		}
	}
     }
	//  stage("Build and Push Docker Image"){
	// 	steps{
	// 		script{
	// 			 docker.withRegistry('',DOCKER_PASS){
	// 			docker_image = docker.build "${IMAGE_NAME}"
	// 			 }
	// 			docker.withRegistry('',DOCKER_PASS){
	// 			docker_image.push("${IMAGE_TAG}")
	// 			}
	// }
 //     }	
 //  }
		stage("Push to DockerHub") {
            steps {
                echo "Here we will write code to push the image to Dockerhub by using withCredentials"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                sh "docker image tag complete-production-e2e-pipeline ${env.dockerHubUser}/complete-production-e2e-pipeline:latest "
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerHubUser}/complete-production-e2e-pipeline:latest"
            }
            }
		}
}
}

























//        stage("Trivy Scan") {
//            steps {
//                script {
// 	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ashfaque9x/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
//                }
//            }
//        }

//        stage ('Cleanup Artifacts') {
//            steps {
//                script {
//                     sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
//                     sh "docker rmi ${IMAGE_NAME}:latest"
//                }
//           }
//        }

//        stage("Trigger CD Pipeline") {
//             steps {
//                 script {
//                     sh "curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-13-232-128-192.ap-south-1.compute.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token'"
//                 }
//             }
//        }
//     }

//     post {
//        failure {
//              emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
//                       subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
//                       mimeType: 'text/html',to: "ashfaque.s510@gmail.com"
//       }
//       success {
//             emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
//                      subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
//                      mimeType: 'text/html',to: "ashfaque.s510@gmail.com"
//       }      
//    }
// }
