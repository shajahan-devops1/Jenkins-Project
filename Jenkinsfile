@Library('jenkins-shared-library') _


def configMap = [
    project: 'roboshop',
    component: 'catalogue'
]

if (env.BRANCH_NAME == 'main') {
    echo "This is the main branch. Proceeding with the pipeline."
}
else {
    echo "This is not the main branch. Running tests."
    testPipeline(configMap)
}
