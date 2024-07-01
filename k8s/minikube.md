# Start minicube
minikube start 

# Get url of the SERVICE
minikube service [SERVICE-NAME] --url

# Create external-ip for loadbalancer service
minikube tunnel & // it starts as process
