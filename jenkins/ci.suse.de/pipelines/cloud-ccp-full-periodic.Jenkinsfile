pipeline {
  /* No idea what I am doing: options? Time trigger? Automatic running of
     cleanup stage if failure on "deployer everything" stage?
  */
  options {
    skipDefaultCheckout() /* skips the clone of automation repo into wrkspc %/
    timestamps()
    timeout(time: 30, unit: 'MINUTES', activity: true)
  }

  agent {
    node {
      label "cloud-ccp-ci"
    }
  }

  stages {
    stage('Deploy everything'){
      steps {
        script {
          sh('''
            set -ex
            export PREFIX=${PREFIX:-'ccpci'}
            export OS_CLOUD=${OS_CLOUD:-'engcloud-cloud-ci-private'}
            export KEYNAME=${KEYNAME:-'engcloud-cloud-ci'}
            export INTERNAL_SUBNET="${PREFIX}-subnet"

            # Make sure the job has a cloud available, and environemnt
            # vars are properly defined
            cat ~/.config/openstack/clouds.yaml
            env

            # Get started
            mkdir ~/ccp/
            pushd ~/ccp
                # No need for branchname, as the full periodic job always tests
                # latest master branch
                git clone --recursive ${ccp_repo} socok8s
                pushd socok8s
                    ./run.sh
                popd
            rm -rf socok8s
            popd

          ''')
        }
      }
    }
  }
  post {
    always {
      script {
        sh('''
          pushd ~/ccp/
            export PREFIX=${PREFIX:-'ccpci'}
            export OS_CLOUD=${OS_CLOUD:-'engcloud-cloud-ci-private'}
            export KEYNAME=${KEYNAME:-'engcloud-cloud-ci'}
            export INTERNAL_SUBNET="${PREFIX}-subnet"

            pushd ~/ccp/socok8s
                ./run.sh teardown
            popd
            rm -rf socok8s
          popd
        ''')
      }
    }
  }
}
