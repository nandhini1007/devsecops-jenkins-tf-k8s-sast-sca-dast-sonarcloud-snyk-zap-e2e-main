pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
	   
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


