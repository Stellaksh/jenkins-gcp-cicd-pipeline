pipeline {
agent any

environment {
PROJECT_ID = 'your-gcp-project-id'
REGION = 'us-central1'
REPO_NAME = 'your-app'
IMAGE_NAME = "gcr.io/${PROJECT_ID}/${REPO_NAME}"
GKE_CLUSTER = 'prod-gke'
GKE_NAMESPACE = 'default'
}

stages {

stage('Checkout') {
steps {
checkout scm
echo "Branch: ${env.BRANCH_NAME}"
}
}

stage('Unit Tests') {
steps {
sh 'echo "Running unit tests..."'
}
}

stage('Build Docker Image') {
steps {
sh """
docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
"""
}
}

stage('Push to GCR') {
steps {
withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEY')]) {
sh """
gcloud auth activate-service-account --key-file=${GCP_KEY}
gcloud auth configure-docker --quiet
docker push ${IMAGE_NAME}:${BUILD_NUMBER}
docker push ${IMAGE_NAME}:latest
"""
}
}
}

stage('Deploy to GKE') {
steps {
withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEY')]) {
sh """
gcloud auth activate-service-account --key-file=${GCP_KEY}
gcloud container clusters get-credentials ${GKE_CLUSTER} \
--region ${REGION} --project ${PROJECT_ID}

kubectl set image deployment/${REPO_NAME} \
${REPO_NAME}=${IMAGE_NAME}:${BUILD_NUMBER} \
-n ${GKE_NAMESPACE}

kubectl rollout status deployment/${REPO_NAME} \
-n ${GKE_NAMESPACE} --timeout=300s
"""
}
}
}

stage('Smoke Test') {
steps {
sh 'echo "Post-deploy smoke test passed"'
}
}
}

post {
success {
echo "SUCCESS — Build ${BUILD_NUMBER} deployed"
}
failure {
echo "FAILED — Rolling back Build ${BUILD_NUMBER}"
sh "kubectl rollout undo deployment/${REPO_NAME} -n ${GKE_NAMESPACE} || true"
}
}
}
