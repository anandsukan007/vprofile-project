pipeline {
    
	agent any
	
	tools {
        maven "maven3"
        jdk "JDK8"
    }
	
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'Vini@123'
        RELEASE_REPO = 'vprofile-release'
	    CENTRAL_REPO   = 'vpro-maven-central'
        NEXUSIP = '172.31.37.64'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
	    SONARSERVER = 'sonarserver'
	    SONARSCANNER = 'sonarscanner'    
    }
	
    stages{
        
        stage('BUILD'){
            steps {
                nexusArtifactUploader(
                nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                credentialsId: ${NEXUS_LOGIN})
                sh 'mvn -s settings.xml -U clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }
	    
	stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
			  scannerHome = tool "${SONARSCANNER}"
          }

          steps {
		  withSonarQubeEnv("${SONARSERVER}") {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }  
	  }
	}
      
    }
}
