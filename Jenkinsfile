library 'ableton-utils@0.13'
library 'groovylint@0.6'

import groovy.transform.Field


@Field final String LINK_ORIGIN = 'https://github.com/Ableton/link.git'
@Field final String LINK_HASH = 'c1449edb28ccba86396a46a9989b8795960160a9'


void addCheckStages() {
  eventRecorder.timedNode('generic-linux') {
    eventRecorder.timedStage('Check') {
      doCheckout()
      groovylint.check('./Jenkinsfile')
    }
  }
}


void addBuildStages(Map args) {
  eventRecorder.timedNode("generic-mac-${args.xcode}") {
    eventRecorder.timedStage('Checkout') {
      sh 'env' // Print out all environment variables for debugging purposes
      doCheckout()

      sh "git clone ${LINK_ORIGIN}"
      dir('link') {
        sh "git checkout ${LINK_HASH}"
        sh 'git submodule update --init --recursive'
      }
    }

    eventRecorder.timedStage('Make Bundle') {
      sh 'make link_dir=link'
      archiveArtifacts 'build/output/LinkKit.zip'
    }
  }
}


void doCheckout() {
  gitRepo.checkoutRevision(
    credentialsId: 'build-ssh-key',
    revision: env.JENKINS_COMMIT,
    url: 'git@github.com:AbletonAppDev/link-ios.git',
  )
}


addCheckStages()
addBuildStages(xcode: 'xcode9.3')
