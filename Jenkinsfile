pipeline {
    agent {
        node {
            label 'maven'
        }
    }

environment {
    PATH = "/opt/apache-maven-3.9.8/bin:$PATH"
}

    stages {
        stage("build") {
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- build completed ----------"
            }
        }
    

        stage("unit test") {
            steps{
                echo "----------- Unit Test started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- Unit Test completed ----------"
            }
        }


        stage("SonarQube analysis") {
            environment{
                scannerHome= tool 'valaxy-sonar-scanner'
            }
            steps {
                withSonarQubeEnv('valaxy-sonarqube-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }  

        stage("Quality Gate") {
            steps {
                script {
                    timeout(time:1,unit:HOURS) {
                        def qg=waitForQualityGate()
                        if (qg.status!='OK'){
                            error "Pipeline aborted due to quality gate failure:${qg.status}"
                        }
                    }
                }
            }
        }

    }
}