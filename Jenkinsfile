#!/usr/bin/env groovy

pipeline {
    agent { label 'lhcbci-cernvm' }
    parameters {
        string(name: 'Pilot_repo', defaultValue: 'DIRACGrid', description: 'The Pilot repo')
        string(name: 'Pilot_branch', defaultValue: 'master', description: 'The Pilot branch')
        string(name: 'DIRAC_test_repo', defaultValue: 'DIRACGrid', description: 'The DIRAC repo to use for getting the test code')
        string(name: 'DIRAC_test_branch', defaultValue: 'rel-v6r20', description: 'The DIRAC branch to use for getting the test code')
        string(name: 'DIRAC_install_repo', defaultValue: 'DIRACGrid', description: 'The DIRAC repo to use for installing DIRAC')
        string(name: 'DIRAC_install_tag', defaultValue: 'v6r20p25', description: 'The DIRAC version to install (tag or branch)')
    }
    environment {
        DIRACSETUP='Dirac-Certification'
        CSURL='dips://lbcertifdirac6.cern.ch:9135/Configuration/Server'
        DIRACSE='CERN-SWTEST'
        JENKINS_QUEUE='jenkins-queue_not_important'
        JENKINS_SITE='DIRAC.Jenkins.ch'
        DIRACUSERDN='/DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=zmathe/CN=674937/CN=Zoltan Mathe'
        DIRACUSERROLE='lhcb_user'
        projectVersion=${params.DIRAC_install_tag}
    }
    stages {
        stage('GET') {
            steps {

                cleanWs()

                echo "Here getting the code"

                dir("$WORKSPACE/TestCode"){
                    git branch: params.Pilot_branch, url: params.Pilot_repo
                    git branch: params.DIRAC_test_branch, url: params.DIRAC_test_repo
                }

                echo "Got the test code"
            }
        }
        stage('SourceAndInstall') {
            steps {
                echo "Sourcing and installing"

                sh """
                    export projectVersion=${params.DIRAC_install_tag}
                    source $WORKSPACE/TestCode/Pilot/tests/CI/pilot_ci.sh
                    fullPilot
                    source $WORKSPACE/PilotInstallDIR/bashrc
                """

                echo "**** Pilot INSTALLATION DONE ****"
            }
        }
        stage('Test') {
            steps {
                echo "Starting the tests"
            }
        }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            /* cleanWs() clean up our workspace */
        }
        success {
            echo 'I succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}