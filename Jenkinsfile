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
        }
	
    stages{
        
        stage('BUILD'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            
        }

	
        }


    }



