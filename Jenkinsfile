pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=sample-devsecops_sampleproject -Dsonar.organization=sample-devsecops -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=14f3f6ef09680ea0682d2869676470aa251bfad9'
			}
		}

	stage('RunSCAAnalysisUsingSnyk') {
            // steps {		
			// 	withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
			// 		sh 'mvn snyk:test -fn'
			// 	}
			// }
			steps {        
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    script {
                        def snykResult = sh(script: 'mvn snyk:test -fn', returnStatus: true)
                        if (snykResult != 0) {
                            currentBuild.result = 'UNSTABLE'
                            createJiraIssue("Snyk identified vulnerabilities in the project. Please review.")
                        }
                    }
                }
            }
    }	
	   
	stage('Jira Notification') {
            steps {
                jiraComment body: 'Pipeline completed. Please review any threats identified.', issueKey: 'THREATS-123'
            }
        }

   }
}
def createJiraIssue(String issueDescription) {
    script {
        httpRequest acceptType: 'APPLICATION_JSON',
                    contentType: 'APPLICATION_JSON',
                    httpMode: 'POST',
                    url: 'https://cloudops-1.atlassian.net/',
                    requestBody: """
                    {
                        "fields": {
                            "project": {
                                "key": "KAN"
                            },
                            "summary": "Threats Identified in Jenkins Pipeline",
                            "description": "${issueDescription}",
                            "issuetype": {
                                "name": "Bug"
                            }
                        }
                    }
                    """,
                    authentication: 'jira-credentials'
    }
}


