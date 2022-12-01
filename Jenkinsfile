pipeline {
    agent none
    options {
    buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
  }

     environment {
       DOCKERHUB_CREDENTIALS=credentials('dockerhub')
  }


    stages {

        stage('Setup parameters') {
            steps {
                script { 
                    properties([
                        parameters([

                            string(
                                defaultValue: '001', 
                                name: 'ImageTAG', 
                                trim: true
                            )
                        ])
                    ])
                }
            }
        }
      




        stage('clean') {
          agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
               sh '''
               rm -rf webapp/target/webapp.war || true
               mvn clean
               '''
            }
        }
       stage('validate') {
           agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
               sh '''
                mvn validate
               '''
            }
        }
       stage('compile') {
           agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
               sh '''
               mvn compile
               '''
            }
        }

       stage('test') {
            agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
            sh''' 
            mvn test
            '''
            }
        }

       stage('package') {
            agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
               sh '''
               mvn package
               ls -ls webapp/target
               pwd
               '''
            }
        }
       stage('verify') {
             agent {
               docker { image 'maven:3.8.6-openjdk-18-slim' }
        }
            steps {
               sh '''
               mvn verify
               '''
            }
        }
       stage('build') {

            steps {
               sh '''
               docker build -t devopseasylearning2021/$ImageTAG .
               '''
            }
        }

       stage('docker Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
       stage('docker push') {
            steps {
               sh '''
               docker push devopseasylearning2021/s3andre:$ImageTAG
               '''
            }
        }
    
     }


}




