pipeline {
     
  agent any
  
  /*Intializing Netstorm test configuration*/
  environment {
def url = "https://216.218.163.75:4432/"
def username = "cavisson"
def password = "@dmin"
def project = "default"
def subProject = "default"
def scenario = "TS1" //testsuite name will be given
def baselineTR = "-1" //Base line test run will be given as 1386
def report = " "
def testMode = "T"
def pollInterval = "60"
  }

  stages {
    stage('Execute Netstorm Test') {
      steps {
        script {
                 echo 'Executing Netstorm test'
                 
				 def automateScripts = ""
				  def nsConnection = new com.cavisson.jenkins.NetStormConnectionManager(url, username, hudson.util.Secret.fromString(password), project, subProject, scenario, testMode, baselineTR, pollInterval);
                 def parameter = env.getEnvironment()
                 
                 echo "${parameter}"
                 
                 parameter.each{ k, v ->
                   echo "${v}"
                   
                   if("${v}".startsWith("NS_SESSION")){
                       def temp = "${v}".split("_")
                      if(temp.length > 2)
                     {  
                       echo temp[2]
                       nsConnection.setDuration(temp[2]);
                     }
                   } else if("${v}".startsWith("NS_NUM_USERS")) {
                        def temp = "${v}".split("_")
                      if(temp.length > 3)
                     {   
                       echo temp[3]
                       nsConnection.setvUsers(temp[3]);
                     }
                   } else if("${v}".startsWith("NS_SERVER_HOST")) {
                        def temp = "${v}".split("_")
                      if(temp.length > 3)
                     {   
                       echo temp[3]
                       nsConnection.setServerHost(temp[3])
                     }
                   } else if("${v}".startsWith("NS_SLA_CHANGE")) {
                        def temp = "${v}".split("_");
                      if(temp.length > 3)
                     {   
                       echo temp[3]
                       nsConnection.addSLAValue(key.toString() , temp [3] )
                     }
                   } else if("${v}".startsWith("NS_RAMP_UP_SEC") || "${v}".startsWith("NS_RAMP_UP_MIN") || "${v}".startsWith("NS_RAMP_UP_HR")) {
                        def temp = "${v}".split("_");
                      if(temp.length > 4)
                     {   
                       echo temp[4]
                       nsConnection.setRampUp(temp[4] + "_" + temp[3])
                     }
                   } else if("${v}".startsWith("NS_AUTOSCRIPT")) {
                        def temp = "${v}".split("_", 3);
                      if(temp.length > 2)
                     {   
                       
					   if(automateScripts.equals(""))
            		     automateScripts = temp[2];
            	       else
            		     automateScripts = automateScripts + "," +temp[2];
                     }
                   } else if("${v}".startsWith("NS_RAMP_UP_DURATION")){
        	            def temp = "${v}".split("_");
                        if(temp.length > 4){
                            echo temp[4]
                            nsConnection.setRampUpDuration(temp[4]);
                        }
                    }
                 }
               
			    nsConnection.setAutoScript(automateScripts)
                 /*
                 * Refrece variable for netstorm builder
                 */
                 def nsBuild = new com.cavisson.jenkins.NetStormBuilder(url, username, password,
                               project, subProject, scenario, testMode, baselineTR, pollInterval)
                 /*
                 * Starting a Netstorm test
                 */                                   
                 report = nsBuild.startTest(nsConnection)  
                 
                 echo report
        }
      }
    }
  }
  post { 
    success { 
      echo 'Going to generate report'
      script {
               step([$class: 'com.cavisson.jenkins.NetStormResultsPublisher', netstormUri: url, 
                       username: username, password: password , project:project, subProject:subProject, scenario:scenario])
               
               echo " report is generate need to append in list "
      }
      
       // publish html
          publishHTML target: [
              allowMissing: false,
              alwaysLinkToLastBuild: true,
              keepAll: true,
              reportDir: 'TestSuiteReport',
              reportFiles: 'TestSuiteReportNS.html',
              reportName: 'HTML Report'
            ]
    }
  }
}
