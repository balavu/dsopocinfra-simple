pipeline {
    agent any 

    environment {
        TERRASCAN_IMAGE = 'tenable/terrascan:latest'
        TERRAFORM_PATH = '.' 
    }

    stages {
        stage("Checkout the Project") {
            steps {
                catchError(buildResult: 'success', stageResult: 'FAILURE') {
                    // Checkout the specific Git repo with your credentials
                    git branch: 'main', credentialsId: 'bala_git_creds', url: 'https://github.com/balavu/dsopocinfra-simple.git'
                    sh '''
                        ls -la
                    '''
                }
            }
        }

        stage('Terrascan Security Scan') {
            steps {
                script {
                    // Pull the latest Terrascan image
                    sh "docker pull $TERRASCAN_IMAGE"

                    // Run Terrascan scan on the Terraform code in the root
                    def terrascanResult = sh(script: """
                        docker run --rm -v ${WORKSPACE}:/iac -w /iac \
                        $TERRASCAN_IMAGE scan -i terraform -d . -o json
                    """, returnStatus: true)

                    // Check the result and fail the build if vulnerabilities are found
                    if (terrascanResult != 0) {
                        error("Terrascan detected security issues in the Terraform code.")
                    }
                }
            }
        }
    }
}
