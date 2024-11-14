# Jenkins Interview Questions and Answers

## Basic Concepts

1. **Q: What is Jenkins and what are its key features?**
   A: Jenkins is an open-source automation server that:
   - Provides continuous integration and delivery
   - Supports distributed builds
   - Offers extensive plugin ecosystem
   - Enables pipeline as code
   - Supports multiple SCM tools

2. **Q: Jenkins architecture components?**
   A: Key components:
   - Master node: Schedules builds, distributes work
   - Agent nodes: Execute builds
   - Executor: Build thread on agent/master
   - Job/Project: Configured automation task
   - Build: Single execution of a job

## Pipeline Basics

1. **Q: Explain Declarative vs Scripted Pipeline**
   A: Examples:

   Declarative Pipeline:
   ```groovy
   pipeline {
       agent any
       
       stages {
           stage('Build') {
               steps {
                   echo 'Building..'
                   sh 'mvn clean package'
               }
           }
           stage('Test') {
               steps {
                   echo 'Testing..'
                   sh 'mvn test'
               }
           }
       }
       
       post {
           always {
               junit '**/target/surefire-reports/*.xml'
           }
       }
   }
   ```

   Scripted Pipeline:
   ```groovy
   node {
       stage('Build') {
           try {
               sh 'mvn clean package'
           } catch (exc) {
               echo 'Build failed'
               throw exc
           }
       }
   }
   ```

2. **Q: Pipeline syntax elements**
   A: Key elements:
   ```groovy
   pipeline {
       // Agent section
       agent {
           docker {
               image 'maven:3.8.1-jdk-8'
           }
       }
       
       // Environment variables
       environment {
           MAVEN_OPTS = '-Xmx3072m'
       }
       
       // Options
       options {
           timeout(time: 1, unit: 'HOURS')
           timestamps()
       }
       
       // Stages and steps
       stages {
           stage('Build') {
               steps {
                   script {
                       def mvnHome = tool 'Maven-3.8.1'
                       sh "${mvnHome}/bin/mvn clean package"
                   }
               }
           }
       }
       
       // Post-build actions
       post {
           success {
               archiveArtifacts artifacts: '**/target/*.jar'
           }
       }
   }
   ```

## Advanced Pipeline Features

1. **Q: How to implement parallel stages?**
   A: Example:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Parallel Stage') {
               parallel {
                   stage('Branch A') {
                       steps {
                           echo "On Branch A"
                       }
                   }
                   stage('Branch B') {
                       steps {
                           echo "On Branch B"
                       }
                   }
               }
           }
       }
   }
   ```

2. **Q: Working with shared libraries**
   A: Example:
   ```groovy
   // In Jenkinsfile
   @Library('my-shared-library') _

   pipeline {
       agent any
       stages {
           stage('Example') {
               steps {
                   mySharedFunction()
               }
           }
       }
   }

   // In shared library (vars/mySharedFunction.groovy)
   def call() {
       sh 'echo "Called from shared library"'
   }
   ```

## Security and Credentials

1. **Q: How to manage credentials?**
   A: Examples:
   ```groovy
   pipeline {
       agent any
       environment {
           // Credentials binding
           AWS_CREDS = credentials('aws-credentials')
       }
       stages {
           stage('Deploy') {
               steps {
                   withCredentials([
                       string(credentialsId: 'api-key', variable: 'API_KEY'),
                       usernamePassword(credentialsId: 'db-creds', 
                                     usernameVariable: 'DB_USER', 
                                     passwordVariable: 'DB_PASS')
                   ]) {
                       sh 'deploy.sh ${API_KEY}'
                   }
               }
           }
       }
   }
   ```

2. **Q: Configure security realms and authorization**
   A: Steps:
   ```groovy
   // Matrix-based security example
   jenkins.model.Jenkins.instance.setSecurityRealm(
       new hudson.security.HudsonPrivateSecurityRealm(false)
   )

   def strategy = new hudson.security.ProjectMatrixAuthorizationStrategy()
   strategy.add(Jenkins.ADMINISTER, "admin")
   strategy.add(Jenkins.READ, "authenticated")
   jenkins.model.Jenkins.instance.setAuthorizationStrategy(strategy)
   ```

## Build and Test Integration

1. **Q: Integration with testing frameworks**
   A: Example:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Test') {
               steps {
                   // JUnit
                   sh 'mvn test'
                   junit '**/target/surefire-reports/*.xml'
                   
                   // Code coverage
                   jacoco()
                   
                   // SonarQube
                   withSonarQubeEnv('SonarQube') {
                       sh 'mvn sonar:sonar'
                   }
               }
           }
       }
   }
   ```

2. **Q: Artifact management**
   A: Example:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Build') {
               steps {
                   sh 'mvn package'
                }
           }
           stage('Archive') {
               steps {
                   // Archive artifacts
                   archiveArtifacts artifacts: '**/target/*.jar'
                   
                   // Publish to Artifactory
                   rtUpload (
                       serverId: 'artifactory',
                       spec: '''{
                           "files": [{
                               "pattern": "target/*.jar",
                               "target": "libs-release-local"
                           }]
                       }'''
                   )
               }
           }
       }
   }
   ```

## Docker Integration

1. **Q: Using Docker in Pipeline**
   A: Examples:
   ```groovy
   pipeline {
       agent {
           docker {
               image 'node:14'
               args '-v $HOME/.npm:/root/.npm'
           }
       }
       
       stages {
           stage('Build') {
               steps {
                   sh 'npm install'
                   sh 'npm run build'
               }
           }
       }
   }

   // Docker image build and push
   pipeline {
       agent any
       stages {
           stage('Build Docker Image') {
               steps {
                   script {
                       docker.build("myapp:${env.BUILD_ID}")
                           .push()
                   }
               }
           }
       }
   }
   ```

## Common Issues and Solutions

1. **Q: Pipeline syntax errors**
   A: Solutions:
   ```groovy
   // Use pipeline syntax generator
   pipeline {
       agent any
       stages {
           stage('Example') {
               steps {
                   script {
                       // Proper string interpolation
                       def version = "1.0"
                       echo "Building version ${version}"
                       
                       // Try-catch for error handling
                       try {
                           sh "./build.sh"
                       } catch (exc) {
                           currentBuild.result = 'FAILURE'
                           error "Build failed: ${exc.message}"
                       }
                   }
               }
           }
       }
   }
   ```

2. **Q: Agent connectivity issues**
   A: Troubleshooting:
   ```groovy
   pipeline {
       agent {
           node {
               label 'specific-agent'
               customWorkspace '/custom/workspace'
           }
       }
       stages {
           stage('Check Agent') {
               steps {
                   sh 'printenv'
                   sh 'whoami'
                   sh 'pwd'
               }
           }
       }
   }
   ```

## Best Practices

1. **Q: Pipeline best practices?**
   A: Examples:
   ```groovy
   pipeline {
       agent any
       
       // Use timeout
       options {
           timeout(time: 1, unit: 'HOURS')
           disableConcurrentBuilds()
       }
       
       // Environment variables
       environment {
           APP_ENV = 'production'
           VERSION = '1.0.0'
       }
       
       // Input validation
       parameters {
           choice(name: 'ENVIRONMENT', 
                 choices: ['dev', 'staging', 'prod'], 
                 description: 'Deployment Environment')
       }
       
       stages {
           // Stage organization
           stage('Validate') {
               steps {
                   script {
                       validateInput()
                   }
           }
       }
       
       // Error handling
       post {
           failure {
               emailext subject: "Build Failed: ${env.JOB_NAME}",
                        body: "Check console output at ${env.BUILD_URL}",
                        to: 'team@example.com'
           }
       }
   }
   ```

2. **Q: Backup and maintenance**
   A: Essential practices:
   ```bash
   # Backup Jenkins home
   tar -czf jenkins_backup.tar.gz /var/lib/jenkins

   # Clean old builds
   find /var/lib/jenkins/jobs/*/builds -mtime +30 -exec rm -rf {} \;

   # Monitor disk space
   df -h /var/lib/jenkins
   ```

## Advanced Configuration

1. **Q: Groovy init scripts**
   A: Example:
   ```groovy
   // init.groovy.d/configure-github.groovy
   import jenkins.model.*
   import org.jenkinsci.plugins.github.*

   def github = Jenkins.instance.getDescriptor(GitHubPluginConfig.class)
   github.setHookUrl('https://jenkins.example.com/github-webhook/')
   github.save()
   ```

2. **Q: Distributed builds configuration**
   A: Example:
   ```groovy
   pipeline {
       agent {
           label 'docker-agent'
       }
       stages {
           stage('Build') {
               steps {
                   // Agent-specific operations
                   sh 'docker info'
               }
           }
       }
   }
   ```