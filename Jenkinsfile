pipeline {
    agent none
    
    tools {
        gradle 'Gradle3'
        maven 'maven3'
        jdk 'OpenJDK-17'
    }
    
    stages {
        
        stage('Preparation') {
            agent {label 'agent1'}
            steps {
                checkout scm
                stash includes: '**', name: 'source'
            }
        }
        
        stage('Build y Javadoc'){
            parallel {
                stage("Build") {
                    agent {label 'agent1'}
                    steps {
                        unstash 'source'
                        sh 'mvn spring-javaformat:apply'
                        sh "mvn clean install -DskipTests=true"
                        sh 'mvn test'
                        junit '**/target/surefire-reports/*.xml'
                        stash includes: '**', name: 'built'
                    }
                }
                stage('Generacion de Javadoc') {
                    agent {label 'agent1'}
                    steps {
                        unstash 'source'
                        sh './gradlew javadoc'
                        stash includes: '**/build/docs/javadoc/**', name: 'javadoc'
                    }
                }
            }
        }
        stage('Deploy') {
            agent { label 'agent2' }
            steps {
                unstash 'built'
                unstash 'javadoc'

                unstash 'built'
                unstash 'javadoc'
                sh 'docker compose up --build -d'
                sh 'sleep 30'
                //TODO: how to run integration tests in gradle or in the project
                sh 'mvn test -Dtest=*IntegrationTests'
                
                junit '**/target/failsafe-reports/*.xml'
                // Aquí irían los pasos para desplegar tu aplicación en Docker, etc.
            }
        }
    }
}
