pipeline {
    agent none
    
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'ubuntu-Appserver-2140'
            }
            steps {
                checkout scm
            }
        }  

        stage('SCA-SAST-SNYK-TEST') {
            agent any
            steps {
                script {
                    snykSecurity(
                        snykInstallation: 'Snyk',
                        snykTokenId: 'synk_api',
                        severity: 'critical'
                    )
                }
            }
        }

        stage('SonarQube Analysis') {
            agent {
                label 'ubuntu-Appserver-2140'
            }
            steps {
                script {
                    def scannerHome = tool 'sonarqube'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=gameapp \
                            -Dsonar.sources=."
                    }
                }
            }
        }

        stage('BUILD-AND-TAG') {
            agent {
                label 'ubuntu-Appserver-2140'
            }
            steps {
                script {
                    def app = docker.build("amalan06/game_app")
                    app.tag("latest")
                }
            }
        }

        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'ubuntu-Appserver-2140'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        def app = docker.image("amalan06/game_app")
                        app.push("latest")
                    }
                }
            }
        }

        stage('DEPLOYMENT') {    
            agent {
                label 'ubuntu-Appserver-2140'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}







// node('ubuntu-Appserver-2140')
// {

// def app
// stage('Cloning Git')
// {
//     /* Let's make sure we have the repository cloned to our workspace */
//     checkout scm
// }

// stage('SNYK-SAST-SCA-TEST')
// {
//      echo "SNYK-SAST-SCA-TEST"   
// }
    
// stage('Build-and-Tag')
// {
//     /* This builds the actual image; 
//          * This is synonymous to docker build on the command line */
//     app = docker.build('amalan06/game_app')
// }

// stage('Post-to-dockerhub')
// {
//     docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
//     {
//         app.push('latest')
//     }
   
// }

// stage('Deploy')
// {
//     sh "docker-compose down"
//     sh "docker-compose up -d"
// }

// }
