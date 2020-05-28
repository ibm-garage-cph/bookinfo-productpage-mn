# Intro

Here you find the new *productpage-v2* service.
This service has only one change compared to *productpage-v1*:
* Changes to include `cover`attribute from `details-v2` service 


## Build
To build the container image locally:
```bash
docker build -t bookinfo-productpage-v2:1 .
```

To push the image to IBM Container Registry Frankfurt ensure you have done these commands:
```bash
ibmcloud login
ibmcloud cr region-set eu-central
ibmcloud cr login
```

Once logged in to the container registry tag the local image and push it up to the docker registry in Frankfurt:
```bash
docker tag bookinfo-productpage-v2:1 de.icr.io/kmddk/bookinfo-productpage-v2:1 
docker push de.icr.io/kmddk/bookinfo-productpage-v2:1 
```

## Deploy manually
First deply the new deployment `productpage-v2`, this will not impact the existing running `productpage-v1`.
```bash
oc apply -f openshift/deployment.yaml
```

And add the new service named `productpage-v2` notice how it has a selector that only sends traffic to pods with `version: v2`:
```bash
oc apply -f openshift/service.yaml
```

We also want to make Istio aware of the new services for productpage. We do this by updating the destination rule:

```bash
oc apply -f openshift/destination-rule-productpage-v1+.yaml
```
And the virtual service. 

```bash
oc apply -f openshift/virtual-services-productpage-v1+.yaml
```

Notice how we are not sending any traffic to `productpage-v2` yet.

### Check port forwarding for
By using portforwarding have a look at how the two services `productpage` and `productpage-v2` gives consistenly different results.

`productpage-v2` does contain the cover image:
```bash
oc port-forward svc/productpage-v2 9080:9080
```

Open http://localhost:9080/productpage  and see the cover page.

Then afterwards check `productpage` and see how it does not container cover image:

```bash
oc port-forward svc/productpage 9080:9080
```

Open http://localhost:9080/productpage to see no cover image.

## Enable 20% traffic to new release

As a deployment option we can now start to send some traffic to the new `productpage-v2`. Lets start by routing 20% of the traffic to this version:

```bash
oc apply -f openshift/virtual-services-productpage-v2+.yaml
```
Reload your bookinfo page 10 times and see 1-3 times the new page with he cover should be displayed.

