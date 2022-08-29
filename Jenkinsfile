pipeline {
  triggers {
    pollSCM ('H/5 * * * *')
  }
  options {
    disableConcurrentBuilds()
  }
  agent {
    node {
      label 'nodejs'
    }
  }
  stages {
    stage ('Validate configuration resources') {
      steps {
        sh 'oc apply --dry-run --validate -k .'
        sh 'oc delete --dry-run -f delete'
      }
    }
    stage ('Apply resources') {
      when {
        branch 'main'
      }
      steps {
        sh 'oc apply -k samples'
        // ignore delete errors caused by templates deleted by previous runs
        sh 'oc delete -f delete --wait || true'
      }
    }
    stage ('Verify deleted templates') {
      when {
        branch 'main'
      }
      steps {
        sh '! oc get templates -n openshift | grep -q rails'
      }
    }
  }
}
