
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
                            if (a.contains("master")) return 1
                            if (b.contains("master")) return -1

                            def a1 = a.split('-')[0].tokenize('.')*.toInteger(), b1 = b.split('-')[0].tokenize('.')*.toInteger()
                            for (i in 0..<[a1.size(), b1.size()].min())
                              if (a1[i] != b1[i]) return a1[i] <=> b1[i]
                            return  0
                          }
                        }
                        // получаем список веток
                        def branches = branchComparator(getBranchNames("Builds/HCS_RELEASE").name).reverse()
                        // получаем предыдущие имена билдов и выдераем имя ветки, тут явно нужна проверка на то что вернулось что-то по-существу
                        // на первом билде будут проблемы, надо проверять это
                        //def last_success_branch = "${currentBuild.previousSuccessfulBuild.displayName.split('#[0-9]+_')[1].split('_$')[0]}"
                        // выкидываем предыдущий ветку из веток
                        //def temp1 = branches.minus([last_success_branch])
                        // добавляем ее на первое место в списке, чтобы упростить usability
                        //def final_branches_list = temp1.add(0, last_success_branch)
                        return branches
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
                        //https://stackoverflow.com/posts/7727217/revisions
                        def versionComparator(versions) {
                          versions.sort {a, b ->
                            // очень грязный хак, исправь меня
                            def a1, b1
                            if (a.matches('^master.+')) {
                               a1 = a.split('master')[1].split('b')[0].tokenize('._#')*.toInteger()
                               b1 = b.split('master')[1].split('b')[0].tokenize('._#')*.toInteger()
                            } else {
                               a1 = a.split('b')[0].tokenize('._#')*.toInteger()
                               b1 = b.split('b')[0].tokenize('._#')*.toInteger()
                            }
                            for (i in 0..<[a1.size(), b1.size()].min())
                              if (a1[i] != b1[i]) return a1[i] <=> b1[i]
                            0
                          }
                        }
                        return versionComparator(getSuccessfulBuilds("Builds", "HCS_RELEASE", Branch)).sort().reverse()
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
