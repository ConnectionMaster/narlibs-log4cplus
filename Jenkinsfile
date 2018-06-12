pipeline {
    
    agent none

	stages {
		stage ('Checkout'){
			parallel {
				stage('Windows') {
					agent { label "windows" }
					steps {
						deleteDir()
						checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: false, parentCredentials: false, recursiveSubmodules: true, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/narlibs-log4cplus']]])
					}
				}
				stage('Unix') {
					agent { label "unix" }
					steps {
						deleteDir()
						checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: false, parentCredentials: false, recursiveSubmodules: true, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/narlibs-log4cplus']]])
					}    
				}
			}
		}
			
		stage('Copy Artifacts') { 
			parallel {
				stage('Windows') {                  
					agent { label "windows" }
					steps {
						copyArtifacts filter: 'msvc14/x64/bin.Release/*.*', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus', target: 'src/nar/resources/aol/amd64-Windows-msvc/lib'
					}
				}
				stage('Unix') {            
					agent { label "unix" }
					steps {
						copyArtifacts filter: 'include/log4cplus/**/*.*', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus', target: 'src/nar/resources/noarch/'
						copyArtifacts filter: '.libs/liblog4cplus-2.0.so', fingerprintArtifacts: true, flatten: true, optional: true, projectName: 'log4cplus', target: 'src/nar/resources/aol/amd64-Linux-gpp/lib/'
						sh 'mv src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-2.0.so src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-nar-2.0.0.so'
						sh 'cp src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-nar-2.0.0.so src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus.so'						
					}					
				}
			}
		}
		
		stage('Package') { 
			agent { label "unix" }
			steps {
				script {
					withMaven(maven: 'Maven-3.2.x', mavenSettingsConfig: 'c2monSettingsConfig') {
				    		sh 'mvn package'
					}
				}						
			}					
		}
		
		//stage('Deploy') { 
		//	agent { label "unix" }
		//	steps {
		//		script {
		//			withMaven(maven: 'Maven-3.2.x', mavenSettingsConfig: 'c2monSettingsConfig') {
		//		    		sh 'mvn deploy'
		//			}
		//		}						
		//	}					
		//}
	}
}
