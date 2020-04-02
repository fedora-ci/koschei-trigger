#!groovy

@Library('fedora-pipeline-library@prototype') _


properties(
    [
        parameters(
            [
                string(description: 'CI message', defaultValue: '', name: 'CI_MESSAGE'),
                string(description: 'SideTag name', defaultValue: '', name: 'SIDETAG_NAME')
            ]
        ),
        pipelineTriggers(
            [[$class: 'CIBuildTrigger',
                noSquash: true,
                providerData: [
                    $class: 'FedMsgSubscriberProviderData',
                    name: 'fedora-fedmsg',
                    overrides: [
                        topic: 'bodhi.update.status.testing.koji-build-group.build.complete'
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


def sidetag = env.SIDETAG_NAME

pipeline {

    agent {
        label 'fedora-ci-agent'
    }

    stages {
        stage('Create Collection in Koschei') {
            steps {
                sh "./create-koschei-collection.sh ${sidetag}"
            }
        }
    }
}
