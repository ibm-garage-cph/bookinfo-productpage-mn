# Intro

# Build

ibmcloud login
ibmcloud cr XXXX
ibmcloud cr login

docker build -t bookinfo-productpage-v2:1 .
docker tag bookinfo-productpage-v2:1 de.icr.io/kmddk/bookinfo-productpage-v2:1 
docker push de.icr.io/kmddk/bookinfo-productpage-v2:1 


# Deploy manually

oc apply -f openshift/deployment.yaml  # adding new productpage-v2 deployment, not impacting running productpage-v1

oc apply -f openshift/service.yaml

oc apply -f openshift/destination-rule-productpage-v1+.yaml

oc apply -f openshift/virtual-services-productpage-v2+.yaml


# Check port forwarding for
oc port-forward svc/productpage-v2 9080:9080

http://localhost:9080/  # cover image

oc port-forward svc/productpage 9080:9080

http://localhost:9080/  # no cover image


# Deploy automatically



