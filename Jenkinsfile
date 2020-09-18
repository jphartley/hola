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
   stage('Master Build') {
       when {
          expression {
            PULL_NUMBER == ''
          }
       }
       steps {
          checkout([$class: 'GitSCM',
          branches: [[name: "master"]],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          gitTool: 'Default',
          submoduleCfg: [],
          userRemoteConfigs: [[refspec: '+refs/pull/*:refs/remotes/origin/pr/*', url: 'https://github.com/hf-bee-bot/hola.git']]])
       }
   }


   stage('PR Build') {
       when {
          expression {
            PULL_NUMBER != ''
          }
       }
       steps {
          checkout([$class: 'GitSCM',
          branches: [[name: "pr/${params.PULL_NUMBER}/head"]],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          gitTool: 'Default',
          submoduleCfg: [],
          userRemoteConfigs: [[refspec: '+refs/pull/*:refs/remotes/origin/pr/*', url: 'https://github.com/hf-bee-bot/hola.git']]])
       }
   }

   stage('Build') {
         steps {
           sh "PULL_NUMBER=${params.PULL_NUMBER} pack --no-color build hferentschik/hello:\${PULL_NUMBER:-latest} --builder paketobuildpacks/builder:tiny --publish"
         }
   }  

   stage('Deploy') {
         steps {
           container('kube') {
             dir("hola/overlays/pr") {
                sh "tmp='${params.PULL_NUMBER}' &&  if [ -z \"\$tmp\" ]; then  export BRANCH=latest; else export BRANCH=\$tmp; fi && envsubst < env.template.yaml > env.yaml"
                sh "PULL_NUMBER=${params.PULL_NUMBER} kustomize edit set image hferentschik/hello=hferentschik/hello:\${PULL_NUMBER:-latest}"
             }
              sh "kustomize build hola/overlays/pr | kubectl apply -n hello -f -"
           }  
         }
   } 
 }
}

