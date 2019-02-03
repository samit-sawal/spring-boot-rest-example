input "ready to integrate?"
node {
    
    try{
    
        stage 'checkout'
		checkout scm
        //git credentialsId: 'b62c2041-e1b3-4634-8b90-0dae187fc917', url: 'https://github.com/samit-sawal/spring-boot-rest-example.git'
    
        stage 'package'
        bat label: '', script: 'mvn clean verify'
        
        stage 'email'
        notify('Success')
        
        stash name:'source',
            excludes:'target/*',
            includes:'**'
    }
    catch(err){
        notify('Error ${err}')
        currentBuild.result = 'FAILURE'
    }
    
    stage 'archive'
    archiveArtifacts 'target/*.war'
    step([$class: 'JUnitResultArchiver', testResults: 'target\\surefire-reports\\TEST-*.xml'])
    publishHTML(target: [allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'target/site/jacoco/', reportFiles: 'index.html', reportName: 'Code Coverage Report', reportTitles: ''])
        
}

/*node('local'){
    bat label: '', script: 'dir'
    unstash 'source'
    bat label: '', script: 'dir'
}*/

parallel agent1:{
    testParallel('local')
},
master:{
    testParallel('')
}

def testParallel(label){
    node(label){
        bat label: '', script: 'dir'
        unstash 'source'
        bat label: '', script: 'dir'
    }   
}

def notify(status){
    emailext (
      to: "samit.sawal@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}