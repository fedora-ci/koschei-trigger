#!groovy

import groovy.json.JsonSlurper

properties(
    [
        pipelineTriggers(
            [[$class: 'CIBuildTrigger',
                noSquash: true,
                providerData: [
                    $class: 'RabbitMQSubscriberProviderData',
                    name: 'FedoraMessaging',
                    overrides: [
                        topic: 'org.fedoraproject.prod.bodhi.update.status.testing.koji-build-group.build.complete',
                        queue: ''
                    ],
                    checks: [
                        [field: '$.artifact.builds', expectedValue: '.*"nvr".*"nvr".*'],
                        [field: '$.artifact.release', expectedValue: 'f33']
                    ]
                ]
            ]]
        )
    ]
)

node('master') {

    print("CI: $CI_MESSAGE")
    
    def slurper = new JsonSlurper()
    def parsed_ci_message = slurper.parseText(CI_MESSAGE)
    def repository = parsed_ci_message.artifact.repository

    print("Repository: ${repository}")

    def command = "curl ${repository}"

    print("\n\nCLI command: ${command}\n\n" )
    
    def proc = command.execute()
    proc.waitFor()
    def parsed_curl_output = slurper.parseText(proc.in.text)
    
    println("\n\nCurl output: ${parsed_curl_output}\n\n" )
    
    def side_tag = parsed_curl_output.update.from_tag
    
    println("\n\nSide tag: ${side_tag}\n\n" )
}