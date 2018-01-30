#!/usr/bin/env groovy

@Library("com.optum.jenkins.pipeline.library@master") _

// This is an example Jenkinfile where we build and then execute a Sonar Scan
// The Jenkins Global Pipeline Library that is being used is: https://github.optum.com/jenkins-pipelines/global-pipeline-library

pipeline {
  agent {
    label 'docker-maven-slave'
  }
  environment {
    GIT_CREDENTIALS_ID = 'dande83'
    TECH_USER ='dande83'
  }

  stages {
    stage ('Dummy') {
      steps {
        echo 'Only sending e-mail.'
      }
    }
    ////stage ('Build') {
    ////  steps {
    ////    glMavenBuild additionalProps:['ci.env':''] // ci.env needed if using UHG parent pom
    ////  }
    ////}
    ////stage('Sonar') {
    ////  steps {
    ////    glSonarMavenScan productName:"DEVOPS", projectName:"CDI_FRAMEWORK-MVP", gitUserCredentialsId:"${env.GIT_CREDENTIALS_ID}"
    ////  }
    ////}
    ////stage('Artifactory') {
    ////  steps {
    ////    glMavenArtifactoryDeploy artifactoryUserCredentialsId:"${env.TECH_USER}"
    ////  }
    ////}
  }
  post {
    always {
      echo 'This will always run'
      emailext body:  "Build URL: ${BUILD_URL} \nJenkins has run the Jenkinsfile which is set to only send an e-mail.",
              subject: "$currentBuild.currentResult-$JOB_NAME",
              to: 'daniel.bullington@optum.com; DIAnderson@optum.com; kaviprasadreddy.koppula@optum.com; sumanth.bodagala@optum.com; wes.charlton@optum.com'
    }
    success {
      echo 'This will run only if successful'
    }
    failure {
      echo 'This will run only if failed'
    }
    unstable {
      echo 'This will run only if the run was marked as unstable'
    }
    changed {
      echo 'This will run only if the state of the Pipeline has changed'
      echo 'For example, if the Pipeline was previously failing but is now successful'
    }
  }
}