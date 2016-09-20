node {

   def image = "kube-registry.kube-system.svc.cluster.local:31000/front-end:${env.BUILD_TAG}"
    
    //how to share stages? We'll have one of these for each microservice...
   stage ("Checkout") {
     git 'https://github.com/amouat/front-end.git'
   }
   
   stage ("Build") {
     sh ("sudo docker build -t ${image} .")
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
       def hash = sh (returnStdout: true, script: "sudo docker push ${image} | grep sha | cut -d ' ' -f 3").trim()
       echo "hash ${hash}"
   }
   
   stage ("Deploy") {
       //Bit on testing
       //Use digest
       
   }
   
   //notifications
   //scanning
   //healthchecks
   //rollbacks (can be done with redeploy)
}
