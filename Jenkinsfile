@Library('JenkisFile-PowerAPICI') import com.powerapi.ESQuery

pipeline {

    agent none
	
	stages {
			
		stage('checkout and reset to branch') {
			agent { label 'master' }
			steps {
				sh 'git checkout $BRANCH_NAME'
				sh 'git reset origin/$BRANCH_NAME --hard'
            }
		}
	
		stage('Build') {
			agent { label 'powerapi' }
			steps {
				sh 'mvn clean install -Dmaven.test.skip=true'
            }
		}
	
		// stage('Test with mvn test and powerapi') {
			// agent { label 'powerapi' }
			// steps {
				// sh '(mvn test & powerapi modules procfs-cpu-simple monitor --frequency 1000 --pids \$! --console duration 40) ' 
				// /* | grep \"muid\" */
			// }
		// }

		stage('Test only powerapi') {
			agent { label 'powerapi' }
			steps {
				script {
					def esQuery = new ESQuery()
					def output = sh (script: 'mvn test & echo $!',returnStdout: true)
					sh "((powerapi duration 30 modules procfs-cpu-simple monitor --frequency 1000 --console --pids ${output}) | grep muid) > data.csv"
					
					
					/* TEST 
						sh "echo import file.."
						def fileDataCSV = new File("data.csv") 
						sh "echo import file OK"
					
					*/				
					
					def csvLine = sh (script: "cat data.csv",returnStdout: true)					
					def fileDataJson = esQuery.csv2jsonString(csvLine)
					 
					println("Le fichier: \n"+fileDataJson)
					sh "curl --header \'content-type: application/JSON\' -XPUT 'http://elasticsearch.app.projet-davidson.fr/powerapi/power/testing' -d '${fileDataJson}'"
				
					
					//sh "curl --header \"content-type: application/JSON\" -XPUT \"http://elasticsearch.app.projet-davidson.fr/powerapi/power/5\" -d@${fileDataJson}"
				}
			}					
		}
		//logstash -f fichierconf.conf
		
		
		//stage('test groovy'){
			//agent { label 'powerapi' }
			//steps {
				//script {
					//def testS = new TestScript()
					//testS.fonction()
				//}
			//}	
		//}	
		/*
		// stage('Sonar') {
			// agent { label 'master' }
			// steps {
				// sh 'mvn sonar:sonar'
			// }
        // }
		/*
		stage ('Deploy to development environment') {
			stage("front") {
				agent { label 'master' }
				steps {
					deploy("uptoyoo-front")
				}
			}
		}
		*/
		
    }
}
		