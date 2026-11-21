pipeline {
    agent any

    parameters {
	string(name: 'REPO_URL', defaultValue: 'https://github.com/KarabaseBarabase/cicd-demo-app.git', description: 'link to fork')
	string(name: 'GIT_BRANCH', defaultValue: 'master', description: 'branch for build')
	booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'run unit-tests')
    }

    stages {
        stage('Checkout') {
	    steps {
		git url: params.REPO_URL, branch : params.GIT_BRANCH
		sh "echo \"Branch: ${params.GIT_BRANCH}\"; echo \"SHA: \$(git rev-parse HEAD)\""
	    }
	}

	stage('Validate') {
	    steps {
	        sh '''
		    for path in README.md requirements.txt src tests; do
		        if [ ! -e "$path" ]; then 
			    echo "error: not existed $path"
			    exit 1
		        fi
		    done
	        '''
	    }
	}

	stage('Test') {
	    when {
		expression {
		    params.RUN_TESTS 
		}
	    }
	    steps {
		sh "python3 -m unittest discover -s tests -v"
	    }
	}

	stage('Build') {
	    steps {
		sh '''   
		    echo "Build num: $BUILD_NUMBER" > build-info.txt
		    echo "Commit sha: $(git rev-parse HEAD)" >> build-info.txt
		    tar -czf app.tar.gz --exclude='.git' .
		'''
	}	
	
	stage('Archive') {
	    steps {
	        archiveArtifacts artifacts: 'app.tar.gz', fingerprint: true
	    }
	}
    }

    post {
	success { echo "Build ${env.BUILD_NUMBER} success" }
	failure { echo "Build ${env.BUILD_NUMBER} failure" }
   }

}
