node {

   def repo = "kube-registry.kube-system.svc.cluster.local:31000/front-end"
   def image = "${repo}:${env.BUILD_ID}"
   def vcsUrl = "https://github.com/amouat/front-end.git" 
   
   stage ("Build") {
     git vcsUrl
     
     def gitHash=sh (returnStdout: true, script: "git rev-parse HEAD").trim()
     def buildDate=sh (returnStdout: true, script: "date --rfc-3339=seconds").trim()
     
     def labels="""--label org.label-schema.schema-version="1.0.0-RC1"\
                   --label org.label-schema.name="front-end"\
                   --label org.label-schema.build-date="${buildDate}"\
                   --label org.label-schema.vcs-ref="${gitHash}"\
                   --label org.label-schema.version="${env.BUILD_ID}"\
                   --label org.label-schema.vcs-url="${vcsUrl}"\
                """
     sh ("sudo docker build ${labels} -t ${image} .")
   }
   
   stage ("Test") {
     echo "Running Container Tests"
     sh ("sudo docker run -e MODE=test ${image}")
     
     echo "Now lots more tests"
     echo "Integration tests; system tests etc"
   }
   
   
   stage ("Push to registry") {
       digest = sh (returnStdout: true, script: "sudo docker push ${image} | grep sha | cut -d ' ' -f 3").trim()
       echo "Pushed image with digest ${digest}"
       
       //Trigger security scan
   }
   
   stage ("Deploy") {
       
       sh ("kubectl set image deployment/front-end front-end=${repo}@$digest")
       
       try {
           
           timeout(time:5, unit:'MINUTES') {
             sh ("kubectl rollout status deployment/front-end")
           }
           
           sh ('[ "200" = $(curl -s -o /dev/null -w "%{http_code}" http://socks.example.com/healthz) ]')
           
       } catch (e) {
           echo "Deployment Failed: ${e}"
           echo "Rolling Back"
           sh ("kubectl rollout undo deployment/front-end")
           error("Failure in Deploy")
       }
   }
   
   stage ("Notify") {
       
       echo "Send Slack message"
   }
 
}
