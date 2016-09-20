node {

   def repo = "kube-registry.kube-system.svc.cluster.local:31000/front-end"
   def image = "${repo}:${env.BUILD_TAG}"
   def vcsUrl = "https://github.com/amouat/front-end.git" 
    
    //how to share stages? We'll have one of these for each microservice...
   stage ("Checkout") {
     git vcsUrl
   }
   
   stage ("Build") {
     sh ("env")
     def gitHash=sh (returnStdout: true, script: "git rev-parse HEAD").trim()
     def buildDate=sh (returnStdout: true, script: "date --rfc-3339=seconds").trim()
     def labels="""--label org.label-schema.name="front-end"\
                   --label org.label-schema.build-date="${buildDate}"\
                   --label org.label-schema.vcs-ref="${gitHash}"\
                   --label org.label-schema.version="${env.BUILD_ID}"\
                """
     sh ("sudo docker build ${labels} -t ${image} .")
   }
   
   stage ("Unit Test") {
     sh ("sudo docker run -e MODE=test ${image}")
   }
   
   stage ("Lots more tests") {
       echo "Integration Tests"
       echo ""
   }
   
   stage ("Push to registry") {
       echo "Lots of labelling goodness"
       echo "Remember to grab the hash"
       hash = sh (returnStdout: true, script: "sudo docker push ${image} | grep sha | cut -d ' ' -f 3").trim()
       echo "hash ${hash}"
   }
   
   stage ("Deploy") {
       //Bit on testing
       sh ("kubectl set image deployment/front-end front-end=${repo}@$hash")
       timeout(time:5, unit:'MINUTES') {
         sh ("kubectl rollout status deployment/front-end")
       }
       
   }
   
   //notifications
   //scanning
   //healthchecks
   //rollbacks (can be done with redeploy)
   //make point about hashes not being nice in deployment
   //parallel
}
