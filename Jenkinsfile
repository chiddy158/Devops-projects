
def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]


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
        CENTRAL_REPO = "vpro-maven-central"
        NEXUSPORT = "8081"
        NEXUSIP = "172.31.59.206"
        NEXUS_GRP_REPO = "vpro-maven-group"
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
        stage('Artifact Upload') {
            steps {
                script {
                    def nexusUrl = "${NEXUSIP}:${NEXUSPORT}"
                    def groupId = 'QA'
                    def version = "${env.BUILD_ID}.${env.BUILD_TIMESTAMP}"
                    def repository = "${RELEASE_REPO}"
                    def credentialsId = "${NEXUS_LOGIN}"
                    def artifactId = 'vproapp'
                    def file = 'target/vprofile-v2.war' // Update this for the correct file type
                    def type = 'war' // Update this for the correct file type

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

    post {
        always {
            echo 'Slack Notification'
            slackSend channel: '#vitalsigna-project',
               color: COLOR_MAP[currentBuild.currentResult],               
               message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}