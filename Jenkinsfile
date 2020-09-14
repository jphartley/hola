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
     string(name: 'BUILD_ID', defaultValue: '', description: 'Build id')
     string(name: 'LIGHTHOUSE_JOB_ID', defaultValue: '', description: 'Lighthouse job id')
 }

 stages {
     stage('Build') {
           steps {
             sh "pack build hello --builder paketobuildpacks/builder:tiny"
           }
     }  
 }
}
