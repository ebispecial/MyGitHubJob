node {

def mvnHome = tool 'Maven3'
stage ('Checkout') {

checkout scm
}
stage ('Build') {
sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
}
stage ('Code Quality Scan') {
withSonarQubeEnv('SonarQube') {
sh "${mvnHome}/bin/mvn sonar:sonar -f MyWebApp/pom.xml"
   }
}
    stage ('Nexus Upload')
    {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '3.14.29.220:8081',
        groupId: 'com.mkyong',
        version: '1.0-SNAPSHOT',
        repository: 'maven-snapshots',
        credentialsId: '0490626f-3ed1-4448-b218-5dda18b39d6a',
        artifacts: [
            [artifactId: 'MyWebApp',
             classifier: '',
             file: 'MyWebApp/target/MyWebApp.war',
             type: 'war']
        ]
     )
    }

   stage ('DEV Deploy')  {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat8(credentialsId: '52fac49e-eb7d-4de5-8e25-44b3c78ce38a', path: '', url: 'http://localhost:8090')], contextPath: null, war: '**/*.war'
    }
stage ('DEV Approve') {
echo "Taking approval from DEV"
timeout(time: 7, unit: 'DAYS') {
input message: 'Do you want to deploy?', submitter: 'admin'
     }
 }


stage ('Slack Notification') {


    slackSend(channel:'general', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")


  }

}
