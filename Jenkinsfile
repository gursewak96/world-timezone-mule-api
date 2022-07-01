pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean install'
      }
    }
    
    stage('Mule Test') {
      steps {
        bat 'mvn test'
      }
    }
    
    stage('Certificate Check') {
    steps {
        echo 'Finding certificate'
		script {
			
			//set the URL of mulesoft application which will parse the keytool output. Change it according to your application, the below might not be operational
			//final String url = "http://hello-world-10072021.us-e2.cloudhub.io/api/certficate"
			final String url = "http://localhost:8081/api/certficate"
			
			//find jks files in the workspace
			def files = findFiles(glob: '**/*.jks')
			
			//this focuses only on the first jks found with [0]. Please loop into all the files and do the below. 
			//When there are no certificate skip the below
			echo """${files[0].name} ${files[0].path}"""
			
			//certificate found, running keytool now. Please use Jenkins credentials to set the store password 
			def certDetails = bat(script : "@keytool -list -v -keystore ${files[0].path} -storepass 123456789", returnStdout: true)
			
			//comment the below later 
			echo "output : ${certDetails}"
			
			
			//set the fileName, appName, orgName and envName dynamically. Currently they are hardcoded.
            def response = httpRequest acceptType: 'APPLICATION_JSON',contentType: 'TEXT_PLAIN',
            				customHeaders: [[name:'fileName',value: 'test1.jks'],[ name:'appName',value: 'customer-prc-api'],[name: 'envName',value: 'Sandbox'],[name: 'orgName',value:'Personal']],
                           httpMode: 'POST', requestBody: "${certDetails}",
                           url: "$url"
            echo "certification details sent"
            echo "${response.status}"
			
	
   			}
		}

	}
    stage('Postman test') {
      steps {
        bat 'newman run .\\src\\test\\resources\\world-timezone-api.postman_collection.json --disable-unicode'
      
      }
    }
  }
}