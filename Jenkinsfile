pipeline {
  agent {
    kubernetes {
      yaml """
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: maven
            image: maven:3-openjdk-8
            command: ['sleep']
            args: ['infinity']
          - name: kaniko
            image: gcr.io/kaniko-project/executor:debug
            command: ['sleep']
            args: ['infinity']
            volumeMounts:
            - name: registry-credentials
              mountPath: /kaniko/.docker
          volumes:
          - name: registry-credentials
            secret:
              secretName: regcred
              items: 
              - key: .dockerconfigjson
                path: config.json
      """
    }
  }

  stages {
    stage('Build and Tag Docker Image') {
      steps {
        container('kaniko') {
          sh "echo {$env.WORKSPACE}"
          sh "executor --dockerfile=Dockerfile \
            --context=dir://${env.WORKSPACE} \
            --cache=true \
            --cache-repo=nexus.ihp001.dev/build-test/cache \
            --destination=nexus.ihp001.dev/build-test:${env.BUILD_NUMBER} \
            --destination=nexus.ihp001.dev/build-test:latest"
        }
      }
    }
  }
}
