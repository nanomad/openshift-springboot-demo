node('maven') {
  stage('Build') {
    git url: "https://github.com/siamaksade/cart-service.git"
    sh "mvn package"
    stash name:"jar", includes:"target/openshift-springboot-demo.jar"
  }
  stage('Test') {
    parallel(
      "A Tests": {
        sh "mvn verify -P cart-tests"
      },
      "B Tests": {
        sh "mvn verify -P discount-tests"
      }
    )
  }
  stage('Build Image') {
    unstash name:"jar"
    sh "oc start-build openshift-demo --from-file=target/openshift-springboot-demo.jar --follow"
  }
  stage('Deploy') {
    openshiftDeploy depCfg: 'openshift-springboot-demo'
    openshiftVerifyDeployment depCfg: 'openshift-springboot-demo', replicaCount: 1, verifyReplicaCount: true
  }
  stage('System Test') {
    sh "curl -s http://openshift-springboot-demo:8080/health | grep 'UP'"
  }
}