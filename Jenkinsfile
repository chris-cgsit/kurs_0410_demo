

def test_Variable = "test"

pipeline {
    // agent any 
    // agent { label 'aws' }
    agent any     

    parameters {
        booleanParam(defaultValue: false,
                     description: 'If checked downstream pipeline is also',
                    name: 'build_downstream'
        )
        string(name: 'STATEMENT',
            defaultValue: 'hello; ls /', description: 'What should I say?'
        )
    }

    tools {
        maven 'maven_3.8.x'  // 'maven_3.6.3'
        jdk 'jdk-1.8.x'    // 'jdk_1.8.0'
    }

    stages {
        stage('Introduction and Pre Checks') {
            steps {
                echo 'This is a minimal pipeline.'
                script {
                    sh 'df -h'
                    sh 'java -version'
                    sh 'mvn --version'
                }
            }
        }
        stage('build') {
            steps {
                echo 'This is the build step'
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Build and Unit Test') {
            steps {
                echo "Build and Unit Test"
                sh "mvn -B -nsu clean install"
            }
            post {
                always {
                  script {
                       try{
                            junit "**/failsafe-reports/*.xml"
                        }catch(Exception e) {
                            echo 'failsafe-reports not found'
                        }
                    }
                }
            }
        }
        stage('Build Downstream Jobs') {
            when {
                expression { params.build_downstream.toBoolean() == true}
            }
            steps {
                echo "build downstream jobs"
                build job: "test", wait: true
            }
        }
        
        
    }
    post {
        failure {
            echo "Build POST  FAILURE action "
        }
        always {
            echo "Build POST action"
        }
    }
}
