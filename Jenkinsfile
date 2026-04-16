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


pipeline {
    agent any
    
    stages {
        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('2. SonarQube Scan') {
            steps {
                // This 'withCredentials' retrieves the token you saved in Jenkins earlier
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube-Local') {
                        bat "C:\\sonar-scanner\\bin\\sonar-scanner.bat " +
                            "-Dsonar.projectKey=my-app-key " +
                            "-Dsonar.sources=. " +
                            "-Dsonar.host.url=http://localhost:9000 " +
                            "-Dsonar.token=${SONAR_TOKEN}" 
                    }
                }
            }
        }
        
        stage("3. Quality Gate Check") {
            steps {
                // Jenkins will now PAUSE here and wait for the ngrok webhook
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
//             }
//         }
//     }
// }
