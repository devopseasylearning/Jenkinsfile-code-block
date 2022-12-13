## Sample Jenkinsfile
```
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```


## Set a global agent
```
pipeline {
    agent any
    agent {
                label ("Node2")
            }
    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```

## Set multiple global agents

```
pipeline {
    agent any
    agent {
                label ("Node1 || Node2 || Node3 || Node4")
            }
    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}

```



# HOW TO SET AGENT 

```
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```

## HOW TO SET buildDiscarder 

```
pipeline {
    agent any
options {
    buildDiscarder(logRotator(numToKeepStr: '20'))
    disableConcurrentBuilds()
    timeout (time: 60, unit: 'MINUTES')
    timestamps()
  }
    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```


## HOW TO SET environment Variable

```
pipeline {
    agent any
  environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}
    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```

## HOW TO SET Parameters
```
pipeline {
    agent any

    stages {

        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                        
                        choice(
                            choices: ['Dev', 'QA', 'Preprod', 'Prod'], 
                            name: 'Environment'
                                 
                                ),


                          string(
                            defaultValue: 'develop',
                            name: 'Area',
                            trim: true
                            ),
                        ])
                    ])
                }
            }
        }
 
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```



## HOW TO SET SonarQube Block

```
pipeline {
    agent any

    stages {
         stage('SonarQube analysis') {
            agent {
                docker {
                  image 'sonarsource/sonar-scanner-cli:4.7.0'
                }
               }
               environment {
        CI = 'true'
        //  scannerHome = tool 'Sonar'
        scannerHome='/opt/sonar-scanner'
    }
            steps{
                withSonarQubeEnv('Sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```


## HOW TO login in dockerhub

```
pipeline {
    agent any
  environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}
    stages {

    stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```

## HOW TO SET condition

```
pipeline {
    agent any

    stages {

        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                        
                        choice(
                            choices: ['Dev', 'QA', 'Preprod', 'Prod'], 
                            name: 'Environment'
                                 
                                ),


                          string(
                            defaultValue: 'develop',
                            name: 'Branch',
                            trim: true
                            ),
                        ])
                    ])
                }
            }
        }

        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }


    stage('build') {
      when{ 
          
          expression {
            env.Environment == 'Dev' }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
                    '''
                }
            }

    stage('test') {
      when{ 
          
          expression {
            env.Environment == 'QA' }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
                    '''
                }
            }

    stage('apply') {
      when{ 
          
          expression {
            env.Branch == 'Devops' }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
                    '''
                }
            }


    stage('test') {
      when{ 
          
          expression {
            env.Branch == 'develop' }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
                    '''
                }
            }


    }
}

```



## HOW TO SET  AND (&&) conditiion

```
pipeline {
    agent any

    stages {

        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                        
                        choice(
                            choices: ['Dev', 'QA', 'Preprod', 'Prod'], 
                            name: 'Environment'
                                 
                                ),


                          string(
                            defaultValue: 'develop',
                            name: 'Branch',
                            trim: true
                            ),
                        ])
                    ])
                }
            }
        }
 
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }


    stage('build') {
      when{ 
          
          expression {
            env.Environment == 'Dev' ||  env.Branch == 'develop'  }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
        '''
                }
            }



    }
}
```



## HOW TO SET  OR (||) conditiion


```
pipeline {
    agent any

    stages {

        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                        
                        choice(
                            choices: ['Dev', 'QA', 'Preprod', 'Prod'], 
                            name: 'Environment'
                                 
                                ),


                          string(
                            defaultValue: 'develop',
                            name: 'Branch',
                            trim: true
                            ),
                        ])
                    ])
                }
            }
        }
 
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }


    stage('build') {
      when{ 
          
          expression {
            env.Environment == 'Dev' &&  env.Branch == 'develop'  }
          
            }
      
     steps {
         sh '''
       ls 
       pwd
        '''
                }
            }



    }
}
```

## Sample post condition slack
```
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }

post {
    
    success {
      slackSend (channel: '#development-alerts', color: 'good', message: "Images  have been pushed to Nexus")
    }

    failure {
      slackSend (channel: '#development-alerts', color: '#FF0000', message: "FAILURE: Images  have NOT been pushed to Nexus")
    }
}



}
```






## Sample post condition with Python interpreter
```
pipeline {
    agent any

    stages {
        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                        
                        choice(
                                    choices: ['Yes', 'No'], 
                                    name: 'deployREDIS'
                           
                                ),

                             string(name: 'WARNTIME',
                             defaultValue: '2',
                            description: '''Warning time (in minutes) before starting upgrade'''),

                          string(
                                defaultValue: 'develop',
                                name: 'Please_leave_this_section_as_it_is',
                                trim: true
                            ),
                        ])
                    ])
                }
            }
        }
 
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }



post {
    always {
      script {
        notifyUpgrade(currentBuild.currentResult, "POST")
      }
    }
    
  }

}



def notifyUpgrade(String buildResult, String whereAt) {
  if (Please_leave_this_section_as_it_is == 'origin/develop') {
    channel = 'development-alerts'
  } else {
    channel = 'development-alerts'
  }
  if (buildResult == "SUCCESS") {
    switch(whereAt) {
      case 'WARNING':
        slackSend(channel: channel,
                color: "#439FE0",
                message: "Challenger: Upgrade starting in ${env.WARNTIME} minutes @ ${env.BUILD_URL}  Application CHALLENGER")
        break
    case 'STARTING':
      slackSend(channel: channel,
                color: "good",
                message: "Challenger: Starting upgrade @ ${env.BUILD_URL} Application CHALLENGER")
      break
    default:
        slackSend(channel: channel,
                color: "good",
                message: "Challenger: Upgrade completed successfully @ ${env.BUILD_URL}  Application CHALLENGER")
        break
    }
  } else {
    slackSend(channel: channel,
              color: "danger",
              message: "Challenger: Upgrade was not successful. Please investigate it immediately.  @ ${env.BUILD_URL}  Application CHALLENGER")
  }
}

```


## Sample post condition slack Multibranch pipeline

```
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }

   post {
   
   success {
      slackSend (channel: '#development-alerts', color: 'good', message: "SUCCESSFUL:  Branch name  <<${env.BRANCH_NAME}>>  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }

 
    unstable {
      slackSend (channel: '#development-alerts', color: 'warning', message: "UNSTABLE:  Branch name  <<${env.BRANCH_NAME}>>  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }

    failure {
      slackSend (channel: '#development-alerts', color: '#FF0000', message: "FAILURE:  Branch name  <<${env.BRANCH_NAME}>> Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
   
    cleanup {
      deleteDir()
    }
}

}
```




## Define agent for a single stage

```
pipeline {
    agent any

    stages {
        agent {
            docker {

              image 'maven:latest'
            }
           }
        stage('Hello') {
            steps {
                sh '''
                ls 
                pwd
                '''
            }
        }
    }
}
```

