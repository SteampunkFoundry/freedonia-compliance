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
           volumes: [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock') ], [hostPathVolume(hostPath: '/home/jenkins/agent/workspace/compliance-masonry-demo_main', mountPath: '/home/jenkins/agent/workspace/compliance-masonry-demo_main')])

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
            container('docker') {
                sh 'pwd'
                sh 'ls -ltr /home/jenkins/agent/workspace/compliance-masonry-demo_main'
                sh 'docker run --rm -v /home/jenkins/agent/workspace/compliance-masonry-demo_main:/opencontrol -w /opencontrol opencontrolorg/compliance-masonry get'
                sh 'docker run --rm -v \"$PWD\":/opencontrol -w /opencontrol opencontrolorg/compliance-masonry docs gitbook FredRAMP-low'
                sh 'docker run --rm -v \"$PWD\":/book -v \"$PWD/pdf\":/pdf -e PDF_NAME=fred.pdf beeronbeard/docker-gitbook-pdf'
                /*
                docker.image('opencontrolorg/compliance-masonry').run('--rm -v \"$PWD\":/opencontrol -w /opencontrol', 'get'){
                }
                docker.image('opencontrolorg/compliance-masonry').run('--rm -v \"$PWD\":/opencontrol -w /opencontrol', 'docs gitbook FredRAMP-low'){
                }
                docker.image('beeronbeard/docker-gitbook-pdf').run('--rm -v \"$PWD\":/book -v \"$PWD/\":/pdf -e PDF_NAME=fred.pdf' ){
                }*/
                archiveArtifacts artifacts: '**/*'
            }
        }
    }
}
