node {

    stage('Startup Clean') {
        sh 'rm -f -r -d *'
        sh 'rm -f -r -d .[!.]* ..?*'
    }

    stage('cloneRepository') {
        checkout scm
    }

    stage('Delete Guestbook App') {
        withKubeConfig([credentialsId: 'kubeconfig-cluster2',
                    caCertificate: '',
                    serverUrl: '',
                    contextName: '',
                    clusterName: '',
                    namespace: ''
                    ]) {
            sh "kubectl delete deployment -l app=redis -n ${env.ns} && \
                kubectl delete service -l app=redis  -n ${env.ns} && \
                kubectl delete deployment frontend  -n ${env.ns} && \
                kubectl delete service frontend  -n ${env.ns} && \
                kubectl delete namespace ${env.ns}"
        }
    }

    stage('Get AporetoC Creds') {
        withCredentials([file(credentialsId: 'aporeto-creds', variable: 'mycreds')]) {
            sh "cp \$mycreds $WORKSPACE/default.creds"
        }       
    }

    stage('Delete Ruleset and Namespace for Guestbook App') {
        sh 'curl -o apoctl https://download.aporeto.com/apoctl/linux/apoctl'
        sh 'chmod +x apoctl'
        withEnv(["APOCTL_CREDS=$WORKSPACE/default.creds"]) {
            sh "./apoctl api delete networkrulesetpolicy \"guestbook backend intra-app\" -n /${env.tenant}/${env.cloudAccount}/${env.group}/${env.ns} && \
                ./apoctl api delete networkrulesetpolicy \"guestbook frontend intra-app\" -n /${env.tenant}/${env.cloudAccount}/${env.group}/${env.ns} && \
                ./apoctl api delete namespace /${env.tenant}/${env.cloudAccount}/${env.group}/${env.ns} -n /${env.tenant}/${env.cloudAccount}/${env.group}"
        }
    }

    stage('End Clean') {
        sh 'rm -f -r -d *'
        sh 'rm -f -r -d .[!.]* ..?*'
    }
}