// pipeline {
//     agent any 
    
//     tools{
//         jdk 'jdk11'
//         maven 'maven3'
//     }
    
//     environment {
//         SCANNER_HOME=tool 'sonar-scanner'
//     }
    
//     stages{
        
//         stage("Git Checkout"){
//             steps{
//                 git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Petclinic.git'
//             }
//         }
        
//         stage("Compile"){
//             steps{
//                 sh "mvn clean compile"
//             }
//         }
        
//          stage("Test Cases"){
//             steps{
//                 sh "mvn test"
//             }
//         }
        
//         stage("Sonarqube Analysis "){
//             steps{
//                 withSonarQubeEnv('sonar-server') {
//                     sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
//                     -Dsonar.java.binaries=. \
//                     -Dsonar.projectKey=Petclinic '''
    
//                 }
//             }
//         }
        
//         stage("OWASP Dependency Check"){
//             steps{
//                 dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
//                 dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//             }
//         }
        
//          stage("Build"){
//             steps{
//                 sh " mvn clean install"
//             }
//         }
        
//         stage("Docker Build & Push"){
//             steps{
//                 script{
//                    withDockerRegistry(credentialsId: '58be877c-9294-410e-98ee-6a959d73b352', toolName: 'docker') {
                        
//                         sh "docker build -t image1 ."
//                         sh "docker tag image1 adijaiswal/pet-clinic123:latest "
//                         sh "docker push adijaiswal/pet-clinic123:latest "
//                     }
//                 }
//             }
//         }
        
//         stage("TRIVY"){
//             steps{
//                 sh " trivy image adijaiswal/pet-clinic123:latest"
//             }
//         }
        
//         stage("Deploy To Tomcat"){
//             steps{
//                 sh "cp  /var/lib/jenkins/workspace/CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "


//             }
//         }
//     }
// }





pipeline {
    agent any
    
    tools {
        // Tells Jenkins to use the Maven we just configured
        maven 'Maven-3'
    }

    stages {
        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }

        stage('2. Build & Test') {
            steps {
                echo 'Running Tests and Generating Coverage Report...'
                // 'test' triggers the unit tests and JaCoCo report generation
                bat "mvn clean verify"
            }
        }

        stage('3. SonarQube Scan') {
            steps {
                script {
                    // Jenkins finds the path for us based on the Nickname!
                    def scannerHome = tool 'SonarScanner-Windows'
                    
                    withCredentials([string(credentialsId: 'Sonar-token', variable: 'SONAR_TOKEN')]) {
                        withSonarQubeEnv('SonarQube-Local') {
                            bat "${scannerHome}\\bin\\sonar-scanner.bat " +
                                "-Dsonar.projectKey=my-app-key " +
                                "-Dsonar.sources=src/main/java " +
                                "-Dsonar.tests=src/test/java " + // Tell it where the tests are
                                "-Dsonar.java.binaries=target/classes " +
                                "-Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml " + // The magic link
                                "-Dsonar.token=${SONAR_TOKEN}"
                        }
                    }
                }
            }
        }

        stage("4. Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
