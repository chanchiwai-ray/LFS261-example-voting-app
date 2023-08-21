pipeline {
  agent none

  stages {
    stage("build") {
      when {
        changeset "**/worker/**"
      }
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-slim'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo "building worker app"
        dir("worker") {
          sh "mvn compile"
        }
      }
    }

    stage("test") {
      when {
        changeset "**/worker/**"
      }
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-slim'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo "Running Unit Tests on worker app"
        dir("worker") {
          sh "mvn clean test"
        }
      }
    }

    stage("package") {
      when {
        branch "master"
        changeset "**/worker/**"
      }
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-slim'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo "Packaging worker app into a jarfile"
        dir("worker") {
          sh "mvn package -DskipTests"
          archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        }
      }
    }

    /* stage("publish") { */
    /*   agent any */
    /*   when { */
    /*     branch "master" */
    /*     changeset "**/worker/**" */
    /*   } */
    /*   steps { */
    /*     echo "Publishing worker app to dockerhub" */
    /*     script { */
    /*       docker.withRegistry("https://index.docker.io/v1", "dockerlogin") { */
    /*         def workerImage = docker.build("raychan96/worker:v${env.BUILD_ID}", "./worker") */
    /*         workerImage.push() */
    /*         workerImage.push("${env.BRANCH_NAME}") */
    /*         workerImage.push("latest") */
    /*       } */
    /*     } */
    /*   } */
    /* } */

    stage('Sonarqube') {
      agent any
      when{
        branch 'master'
      }
      // tools {
       // jdk "JDK11" // the name you have given the JDK installation in Global Tool Configuration
     // }

      environment{
        sonarpath = tool 'SonarScanner'
      }

      steps {
        echo 'Running Sonarqube Analysis..'
        withSonarQubeEnv('sonar-instavote') {
          sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
        }
      }
    }


    stage("Quality Gate") {
      steps {
        timeout(time: 1, unit: 'HOURS') {
            // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
            // true = set pipeline to UNSTABLE, false = don't
            waitForQualityGate abortPipeline: true
        }
      }
    }

    stage("deploy") {
      agent any
      when {
        branch "master"
      }
      steps {
        echo "Deploying instavote app with docker compose"
        sh "docker compose up -d"
        }
      }
    }
  }

  post {
    always {
      echo "The job is completed."
    }
  }
}
