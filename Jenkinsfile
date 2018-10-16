pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Build') {
          steps {
            sh '''#!/bin/bash

echo "*******-Starting CI CD Pipeline Tasks-*******"

#-BUILD
echo ""
echo "..... Build Phase Started :: Compiling Source Code :: ....."
cd java_web_code
mvn install'''
          }
        }
        stage('Test') {
          steps {
            sh '''#!/bin/bash #-BUILD (TEST)
echo ""
echo "..... Test Phase Started :: Testing via Automated Scripts :: ....."
cd java_web_code mvn clean verify -P integration-test'''
          }
        }
        stage('cotainer') {
          steps {
            sh '''#!/bin/bash
#-POSTBUILD (PROVISIONING DEPLOYMENT)
echo ""
echo "..... Integration Phase Started :: Copying Artifacts :: ....."
cd java_web_code
/bin/cp target/wildfly-spring-boot-sample-1.0.0.war ../docker/
echo ""
echo "..... Provisioning Phase Started :: Building Docker Container :: ....."
cd ../docker/
sudo docker build -t devops_pipeline_demo .'''
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        sh '''#!/bin/bash
#-DELIVERY (APPS DEPLOYMENT)

CONTAINER=devops_pipeline_demo

RUNNING=$(sudo docker inspect --format="{{ .State.Running }}" $CONTAINER 2> /dev/null)
if [ $? -eq 1 ]; then
  echo "\'$CONTAINER\' does not exist."
else
  sudo docker rm -f $CONTAINER
fi

    # run your container
    echo ""
    echo "..... Deployment Phase Started :: Building Docker Container :: ....."
    sudo docker run -d -p 8180:8080 --name devops_pipeline_demo devops_pipeline_demo

#-Completion
echo "------------------------------------------------------"
echo "View App deployed here: http://jenkins:8180/sampel.txt"
echo "------------------------------------------------------"
'''
      }
    }
  }
}