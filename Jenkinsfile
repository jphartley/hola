pipeline {
 agent {
     kubernetes {
       label 'build-pod'
       idleMinutes 5
       yamlFile 'build-pod.yaml'
       defaultContainer 'pack'
     }
 }

 parameters {
     string(name: 'JOB_NAME', defaultValue: '', description: 'Job name')
     string(name: 'JOB_TYPE', defaultValue: '', description: 'Job type')
     string(name: 'JOB_SPEC', defaultValue: '', description: 'Job spec')


     string(name: 'PULL_BASE_REF', defaultValue: '', description: 'Base ref')
     string(name: 'PULL_REFS', defaultValue: '', description: 'Pull refs')
     string(name: 'PULL_REFS', defaultValue: '', description: 'Pull base refs')
     string(name: 'PULL_NUMBER', defaultValue: '', description: 'Pull number')
     string(name: 'PULL_BASE_SHA', defaultValue: '', description: 'Pull base SHA')
     string(name: 'PULL_PULL_SHA', defaultValue: '', description: 'Pull pull SHA')

     string(name: 'BUILD_ID', defaultValue: '', description: 'Build id')
     string(name: 'LIGHTHOUSE_JOB_ID', defaultValue: '', description: 'Lighthouse job id')
 }


 stages {
   stage('Checkout') {
       when {
          expression {
            PULL_NUMBER != ''
          }
       }
       steps {
          checkout([$class: 'GitSCM',
          branches: [[name: "pr/${params.PULL_REQUESTS}/head"]],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          gitTool: 'Default',
          submoduleCfg: [],
          userRemoteConfigs: [[refspec: '+refs/pull/*:refs/remotes/origin/pr/*', url: 'https://github.com/hf-bee-bot/hola.git']]])
       }
   }

   stage('Build') {
         steps {
           sh "pack build hello --builder paketobuildpacks/builder:tiny"
         }
   }  
 }
}

