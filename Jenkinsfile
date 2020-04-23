#!groovy

/*
Jenkins: https://jenkins-continuous-infra.apps.ci.centos.org/ 
*/

@Library('fedora-pipeline-library@prototype') _

properties(
    [
        parameters(
            [
                string(description: 'CI message', defaultValue: '', name: 'CI_MESSAGE'),
                string(description: 'SideTag name', defaultValue: '', name: 'SIDETAG_NAME'),
                string(description: 'Koji target', defaultValue: 'rhel-8.0.0-candidate', name: 'KOJI_TARGET')
            ]
        ),
        pipelineTriggers(
            [[$class: 'CIBuildTrigger',
                noSquash: true,
                providerData: [
                    $class: 'FedMsgSubscriberProviderData',
                    name: 'fedora-fedmsg',
                    overrides: [
                        topic: 'org.fedoraproject.prod.bodhi.update.status.testing.koji-build-group.build.complete'
                    ],
                    checks: [
                        [field: '$.artifact.builds', expectedValue: '.*"nvr".*"nvr".*'],
                        [field: '$.artifact.release', expectedValue: 'f33'],
                    ]
                ]
            ]]
        )
    ]
)

jobItem.setDescription(
"""This job triggers by a UMB message and creates a 'collection' in the staging Koschei
    <br>
    <br>Staging Koschei: https://koschei-stage.osci.redhat.com/
<br>""")


def sidetag = env.SIDETAG_NAME
def koji_target = env.KOJI_TARGET

pipeline {

    agent {
        label 'fedora-ci-agent'
    }

    stages {
        stage('Create Collection in Koschei') {
            steps {
                script {
        		    openshift.withCluster( 'https://console.app.os.stg.fedoraproject.org:443' ) {
        		        openshift.withProject( 'sturivny-koschei-playground' ) {
        			        def pod = openshift.raw( "get pods | awk '/admin/{print \$1}'" ).out
        			        String admin_pod = pod.trim()
        			        echo "Pod name: ${admin_pod}"
        			        def cli_command = openshift.raw( "exec ${admin_pod} -i -t -- koschei-admin create-collection ${sidetag} -d ${sidetag} -t ${koji_target}" )
        			        echo "Executed command: ${cli_command}"
    		            }
		            }
		        }
            }
        }
    }
}
