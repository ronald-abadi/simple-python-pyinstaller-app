node {
    docker.image('python:2-alpine').inside() {
        stage('Build') {
            echo 'submission-cicd-pipeline-rabadi-python: Build stage in progress...'
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    docker.image('qnib/pytest').inside() {
        stage('Test') {
            echo 'submission-cicd-pipeline-rabadi-python: Test stage in progress...'
            checkout scm
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'	
        }
    }    
    docker.image('six8/pyinstaller-alpine').inside("--entrypoint=''") {
        stage('ManualApproval') {
            echo 'submission-cicd-pipeline-rabadi-python: Manual Approval stage in progress...'
            script {
                try {
                    env.theInput = input message: 'Lanjutkan ke tahap Deploy?'
                    env.theInput = 'Proceed'
                } catch(e) {
                    env.theInput = 'Abort'                    
                }
            }
        }
        stage('Deploy') {
            if (env.theInput == 'Proceed') {
                echo 'submission-cicd-pipeline-rabadi-python: Deploy stage in progress...'
                echo '(Note: using six8/pyinstaller-alpine, because cdrx/pyinstaller-linux:python2 has problems)'
                sh 'pyinstaller --onefile sources/add2vals.py'
                archiveArtifacts 'dist/add2vals'
                sh 'sleep 1m'
                echo 'done...'
            } else {
                echo 'submission-cicd-pipeline-rabadi-python: Approval is rejected and Deployment is cancelled...'
                sh 'sleep 10'
                echo 'done...'
            }
        }
    }
}
