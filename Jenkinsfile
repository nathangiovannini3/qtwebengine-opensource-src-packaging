pipeline {
  agent any
  stages {
    stage('Build source') {
      steps {
        sh '/usr/bin/build-source.sh'
        stash(name: 'source', includes: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo,lintian.txt')
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
      }
    }
    stage('Build binary - armhf') {
      steps {
        parallel(
          "Build binary - armhf": {
            node(label: 'armhf') {
              cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
              unstash 'source'
              sh '''export architecture="armhf"
              export SKIP_ARCH_BUILD=false
              build-binary.sh'''
              stash(includes: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo,lintian.txt', name: 'build-armhf')
              cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
            }
          }
        )
      }
    }
    stage('Results') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
        unstash 'build-armhf'
        archiveArtifacts(artifacts: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo', fingerprint: true, onlyIfSuccessful: true)
        sh '''/usr/bin/build-repo.sh'''
      }
    }
    stage('Cleanup') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
      }
    }
  }
}
