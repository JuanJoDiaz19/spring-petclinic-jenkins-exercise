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
                        junit '**/build/test-results/test/*.xml'
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
    }
}
