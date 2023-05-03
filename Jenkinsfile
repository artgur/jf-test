
properties([
    parameters([
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Select HCS_RELEASE Branch',
            filterLength: 1,
            filterable: true,
            name: 'Branch',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: false,
                    script:
                        'return["Could not get Build"]'
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script: '''
                        import jenkins.model.Jenkins
                        def getBranchNames(projectName){
                            def branches = []
                            Jenkins.instance.getAllItems(org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProject).each { multiBranch ->
                              if (multiBranch.fullName == projectName ) {
                                  multiBranch.allJobs.each { job ->
                                    branches.add(job)
                                  }
                              }
                            }
                            branches.unique();
                        }
                        def branchComparator(branches) {
                          branches.sort {a, b ->
                            if (a.contains("master")) {
                              return 1
                            } else if (b.contains("master")) {
                              return -1
                            }
                            def a1 = a.split('-')[0].tokenize('.')*.toInteger(), b1 = b.split('-')[0].tokenize('.')*.toInteger()
                            for (i in 0..<[a1.size(), b1.size()].min())
                              if (a1[i] != b1[i]) return a1[i] <=> b1[i]
                            return  0
                          }
                        }
                        return branches = branchComparator(getBranchNames("Builds/HCS_RELEASE").name).reverse()
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Select HCS_RELEASE Build',
            filterLength: 1,
            filterable: true,
            name: 'Build',
            referencedParameters: 'Branch',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: false,
                    script:
                        'return["Could not get Build"]'
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script:
                        '''
                        import jenkins.model.Jenkins
                        def getSuccessfulBuilds(folder, projectName, branchName) {
                            def builds = []
                            def job = Jenkins.instance.getItem(folder).getItem(projectName).getBranch(branchName)
                            job.builds.each { build ->
                                if (build.getResult().toString().equals("SUCCESS")) {
                                    builds.add(build.getDisplayName())
                                }
                            }
                            builds.unique();

                        }
                        getSuccessfulBuilds("Builds", "HCS_RELEASE", Branch)
                        '''
                ]
            ]
        ],
    ])
])

pipeline {
    agent any
    options {
        timestamps ()
        parallelsAlwaysFailFast()
    }
    stages {
        stage("Setting BuildName") {
            steps {
              echo "Hello world"
            }
        }
    }
}
