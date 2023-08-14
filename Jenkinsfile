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
  }

  post {
    always {
      echo "The job is completed."
    }
  }
}