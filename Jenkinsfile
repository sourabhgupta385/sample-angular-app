def readProperties(){
	def properties_file_path = "${workspace}" + "@script/properties.yml"
	def property = readYaml file: properties_file_path

    env.APP_NAME = property.APP_NAME
    env.MS_NAME = property.MS_NAME
    env.BRANCH = property.BRANCH
    env.GIT_SOURCE_URL = property.GIT_SOURCE_URL
    env.GIT_CREDENTIALS = property.GIT_CREDENTIALS
    env.CODE_QUALITY = property.CODE_QUALITY
    env.UNIT_TESTING = property.UNIT_TESTING
    env.CODE_COVERAGE = property.CODE_COVERAGE
    env.FUNCTIONAL_TESTING = property.FUNCTIONAL_TESTING
    env.LOAD_TESTING = property.LOAD_TESTING
}



node{

	stage('Read Properties') {
        readProperties()
    }
	
	def NODEJS_HOME = tool "NODE_PATH"
	env.PATH="${env.PATH}:${NODEJS_HOME}/bin"
   
	stage('Checkout'){
       checkout([$class: 'GitSCM', branches: [[name: "*/${BRANCH}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${GIT_CREDENTIALS}", url: "${GIT_SOURCE_URL}"]]])
       env.WORKSPACE = "${workspace}"
	}
  
    stage('Initial Setup'){
        sh 'npm install'
    }
   
    if(env.UNIT_TESTING == 'True'){
        stage('Unit Testing'){   
            sh ' $(npm bin)/ng test -- --no-watch --no-progress --browsers Chrome_no_sandbox'
   	    }
    }
  
    if(env.CODE_COVERAGE == 'True'){
        stage('Code Coverage'){	        
	        sh ' $(npm bin)/ng test -- --no-watch --no-progress --code-coverage --browsers Chrome_no_sandbox'
   	    }
    }
   
    if(env.CODE_QUALITY == 'True'){
        stage('Code Quality Analysis'){ 
            sh 'npm run lint'
        }
    }
	
	stage('Build Application'){
		sh "npm run build"
	}
	
	if(env.LOAD_TESTING == 'True'){
        stage("Load Testing"){
            sh 'artillery run -o load.json perfTest.yml' 
        }
    }
}	
    
