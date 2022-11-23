# ansible-config-mgt-14

# jenkins for quick task and simulation of steps
```
pipeline {
    agent any

  stages {

    //Initial clean up-Check if thr workspace is existing 
     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    //ends here
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    stage('package'){
     steps{
        script{
            sh 'echo "Packaging App"'
        }
     }  
    }
//deploy
    stage('deploy'){
        steps{
            script{
               sh 'echo "Deploy to Dev"' 
            }
        }
    }
   stage('Clean Workspace after build'){
        steps{
          cleanWs()
     }
    }
}
}
```
