pipeline {
    
	agent any
	
	tools {
        maven "Maven3"
        jdk "OracleJDK8"
    }
	
    environment {
        SNAP_REPO = "vprofile-snapshot"
        NEXUS_USER = "admin"
        NEXUS_PASS = "select77"
        RELEASE_REPO = "vprofile-release"
        CENTRAL_REPO = "vprofile-maven-central"
        NEXUSPORT = "8081"
        NEXUSIP = "172.31.62.254"
        NEXUS_GRP_REPO = "vprofile-maven-group"
        NEXUS_LOGIN = "nexuslogin"
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"
    }
	
    stages{
        
        stage('Build'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
            
        }
        
        stage('Test'){
            steps {
                sh 'mvn -s settings.xml test'
            }
        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        stage('Sonarqube Analysis') {          
            environment {
                scannerHome = tool name: SONARSCANNER, type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            steps {
                withSonarQubeEnv(SONARSERVER){
                    sh """${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile-repo \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportPaths=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml"""
                }

                
            }
        }
        stage('Artifact Upload'){
            steps {
                script {
                    def nexusUrl = "${NEXUSIP}:${NEXUSPORT}"
                    def groupId = 'QA'
                    def version = "${env.BUILD_ID}.${env.BUILD_TIMESTAMP}"
                    def repository = "${RELEASE_REPO}"
                    def credentialsId = "${NEXUS_LOGIN}"
                    def artifactId = 'viproapp'
                    def file = 'target/vprofile-v2.war'
                    def type = 'jar'

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: nexusUrl,
                        groupId: groupId,
                        version: version,
                        repository: repository,
                        credentialsId: credentialsId,
                        artifacts: [
                            [artifactId: artifactId,
                            classifier: '',
                            file: file,
                            type: type]
                        ]
                   )
                }
        }
    }

    }

}
