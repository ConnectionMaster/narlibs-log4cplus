pipeline {
    agent none
    stages {
        stage ('Checkout'){
            parallel {
                stage('Windows') {
                    agent { label "windows" }
                    steps {
                        deleteDir()                        
                        checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: true, parentCredentials: false, recursiveSubmodules: false, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/narlibs-log4cplus']]])
                    }
                }
                stage('Unix') {
                    agent { label "unix" }
                    steps {
                        deleteDir()
                        checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: true, parentCredentials: false, recursiveSubmodules: false, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/narlibs-log4cplus']]])
                    }    
                }
            }
        }
            
        stage('Copy Artifacts') { 
            parallel {
                stage('Windows') {                  
                    agent { label "windows" }
                    steps {
                        copyArtifacts filter: 'msvc14/x64/bin.Release/*.*', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus/log4cplus/2.0.x', selector: lastSuccessful(), target: 'src/nar/resources/aol/amd64-Windows-msvc/lib'
                    }
                }
                stage('Unix') {            
                    agent { label "unix" }
                    steps {
                        copyArtifacts filter: 'include/log4cplus/**/*.*', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus/log4cplus/2.0.x', selector: lastSuccessful(), target: 'src/nar/resources/noarch/'
                        copyArtifacts filter: '.libs/liblog4cplus-2.0.so', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus/log4cplus/2.0.x', selector: lastSuccessful(), target: 'src/nar/resources/aol/amd64-Linux-gpp/lib/'
                        sh 'mv src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-2.0.so src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-nar-2.0.0.so'
                        sh 'cp src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-nar-2.0.0.so src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus.so'                        
                    }                    
                }
            }
        }
        
        stage('Package') { 
            parallel {
                stage('Windows') {                  
                    agent { label "windows" }
                    steps {
                        withMaven(jdk: 'Java 11 (Windows)', maven: 'Maven-3.2.x', mavenSettingsConfig: 'CERN-EN-ICE') {
                            bat '''
                                call "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\VC\\vcvarsall.bat" amd64
                                mvn package
                            '''                            
                          }
                    }
                }
                stage('Unix') {          
                    agent { label "unix" }
                    steps {
                       withMaven(maven: 'Maven-3.2.x', mavenSettingsConfig: 'CERN-EN-ICE') {
                                  sh 'mvn package'
                       }
                    }
                }
                 
            }
        }
        
        stage('Deploy') { 
            parallel {
                stage('Windows') {                  
                    agent { label "windows" }
                    steps {
                        withMaven(jdk: 'Java 11 (Windows)', maven: 'Maven-3.2.x', mavenSettingsConfig: 'CERN-EN-ICE') {
                                bat '''
                                    call "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\VC\\vcvarsall.bat" amd64
                                    mvn deploy
                                '''
                        }
                     }
                }
                stage('Unix') {     
                     agent { label "unix" }
                     steps {
                        withMaven(maven: 'Maven-3.2.x', mavenSettingsConfig: 'CERN-EN-ICE') {
                                sh 'mvn deploy'
                        }
                     }
                }
           
          }
      }
   }
}
