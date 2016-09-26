#!groovy

node() {
    currentBuild.result = "SUCCESS"
    try {
       stage 'Checkout'
            checkout scm
            def names = nodeNames()
            print "Name of Nodes: ${names}"
       stage 'Test'
            env.NODE_ENV = "test"
            print "Environment will be : ${env.NODE_ENV}"
            print "Branch name: ${env.BRANCH_NAME}"
            bash 'node -v'
            bash 'npm prune'
            bash 'npm install'
            bash 'npm test'
       stage 'Build Docker'
            bash './dockerBuild.sh'
       stage 'Deploy'
            echo 'Push to Repo'
            bash './dockerPushToRepo.sh'
            echo 'ssh to web server and tell it to pull new image'
            bash 'ssh <user>@<host> <path-to>/dockerRun.sh'
       stage 'Cleanup'
            echo 'prune and cleanup'
            bash 'npm prune'
            bash 'rm node_modules -rf'
            mail body: 'project build successful',
                        from: 'saidsef@gmail.com',
                        replyTo: 'saidsef@gmail.com.com',
                        subject: 'project build successful',
                        to: 'saidsef@gmail.com'
        }
    catch (err) {
        currentBuild.result = "FAILURE"
            mail body: "project build error is here: ${env.BUILD_URL}" ,
            from: 'saidsef@gmail.com',
            replyTo: 'saidsef@gmail.com',
            subject: 'project build failed',
            to: 'saidsef@gmail.com'
        throw err
    }
}

// This method collects a list of Node names from the current Jenkins instance
@NonCPS
def nodeNames() {
  return jenkins.model.Jenkins.instance.nodes.collect { node -> node.name }
}
