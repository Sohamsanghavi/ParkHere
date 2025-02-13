pipeline {
    agent any

    stages {
        stage('Pulling Application from Github Repo') {
            steps {
                git credentialsId: 'git-credss', url: 'https://github.com/Sohamsanghavi/ParkHere.git', branch: 'main'
            }
        }
        stage('Frontend:  Node Package Manager'){
            steps{
              sh '''
							cd ParkHere/frontend-web-react
							npm ci
					'''
            }
        }
        stage('Backend: Testing and Installing Dependencies (Node Package Manager)'){
            steps{
               sh '''
							cd ParkHere/backend
							npm ci
							npm run test
					'''
            }
        }
        stage('Frontend: Build Docker Image') {
			steps {
				sh "docker build -t Sohamsanghavi/ParkHere-frontend-web:latest ParkHere/frontend-web-react/"
			}   
		}
        stage('Backend: Build Docker Image') {
			steps {
				sh "docker build -t Sohamsanghavi/ParkHere-backend:latest ParkHere/backend/"
			}   
		}
		stage('Docker LOGIN'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker-auth', variable: 'dockerhubcreds')]) {
                        sh 'docker login -u Sohamsanghavi -p ${dockerhubcreds}'
                    }
                }
            }
        }
        stage('FRONTEND Image PUSH'){
            steps{
                 sh 'docker push Sohamsanghavi/ParkHere-frontend-web:latest'
            }
        }
        stage('BACKEND Image PUSH'){
            steps{
                 sh 'docker push Sohamsanghavi/ParkHere-backend:latest'
            }
        }
        stage('Removing Docker Images (frontend) from Local') {
			steps {
				sh "docker rmi Sohamsanghavi/ParkHere-frontend-web:latest"
			}
		}
		stage('Removing Docker Images (backend) from Local') {
			steps {
				sh "docker rmi Sohamsanghavi/ParkHere-backend:latest"
			}
		}
		stage('Ansible'){
            steps{
                sh 'ls'
                ansiblePlaybook becomeUser: null, installation: 'Ansible', inventory: 'ParkHere/inventory', playbook: 'ParkHere/deploy-playbook.yml', sudoUser: 'null'
            }
        }
    }
}
