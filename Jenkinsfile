def label = "ImageBuildPod-${UUID.randomUUID().toString()}"
podTemplate(
       label: label,
       containers: [
           containerTemplate(name: 'docker',
                               image: 'docker:latest',
                               ttyEnabled: true,
                               command: 'cat',
                               envVars: [containerEnvVar(key: 'DOCKER_HOST', value: "unix:///var/run/docker.sock")],
                               privileged: true)
                   ],
           volumes: [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock') ])

{
    node(label) {
        stage('Clean Workspace') {
            cleanWs()
        }
        stage("checkout") {
            checkout(
                [
                    $class                           : 'GitSCM',
                    branches                         : scm.branches,
                    doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                    extensions                       : scm.extensions,
                    submoduleCfg                     : [],
                    userRemoteConfigs                : scm.userRemoteConfigs
                ]
            )
        }
        stage('Install Packages') {
            container('docker'){
             docker.image('opencontrolorg/compliance-masonry get').withRun(' -v \"$PWD\":/opencontrol -w /opencontrol') {  c ->
                  sh 'pwd'
                  sh 'ls -ltr components/'
                  sh 'ls -ltr components/AU_policy/'
                  sh 'tar -cvf components.tar components'

              }
                archiveArtifacts artifacts: '*'

            }
        }
    }
}
