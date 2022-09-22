pipeline {
    agent {
        kubernetes {
            yaml """
kind: Pod
spec:
  containers:
  - name: kubectl
    image: sergeytim/task:kubectl_helm
    volumeMounts:
      - name: config-for-jenkins
        mountPath: /home/tsr/task/Jenkins/config_for_jenkins
    tty: true
  - name: jnlp
    image: jenkins/inbound-agent:4.7-1
    env:
    - name: GIT_SSL_NO_VERIFY
      value: "1"
  volumes:
  - name: config-for-jenkins
    secret:
      secretName: config-for-jenkins
      items:
      - key: config
        path: config
      - key: ca.crt
        path: ca.crt
      - key: client.crt
        path: client.crt
      - key: client.key
        path: client.key
"""
            }
        }

    stages {
        stage ('delete task') {
            steps {
              container('kubectl') {
                script {
                  delTask=sh(script: "kubectl --kubeconfig=/home/tsr/task/Jenkins/config_for_jenkins/config delete ns task", returnStdout: true)
                    println(delTask)
                    }
                }
              }
            }
        stage ('kubectl get pod') {
            steps {
                container('kubectl') {
                    script {
                          podsName=sh(script: "kubectl --kubeconfig=/home/tsr/task/Jenkins/config_for_jenkins/config get pods -n jenkins", returnStdout: true)
                            println(podsName)
                    }
                }
            }
        }
        stage ('helm install') {
            steps {
              container('kubectl') {
                script {
                  deploy=sh(script: "helm install task task-chart --namespace task --create-namespace", returnStdout: true)
                    println(deploy)
                    sleep(10)
                    }
                }
              }
            }
        stage ('helm list') {
            steps {
              container('kubectl') {
                script {
                  list=sh(script: "helm list -n task", returnStdout: true)
                    println(list)
                    }
                }
              }
            }
        stage ('Все что есть в ns task после деплоя') {
            steps {
              container('kubectl') {
                script {
                  podsTask=sh(script: "kubectl --kubeconfig=/home/tsr/task/Jenkins/config_for_jenkins/config get all -n task", returnStdout: true)
                    println(podsTask)
                    }
                }
              }
            }
        
    }
}