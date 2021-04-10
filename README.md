# Project Title

eToro DevOps technical task

## Description
eToro DevOps technical task to deplloy simple-web application using Helm & Jenkins pipeline.

### Dependencies
1. Install **az cli**:  https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt2.
2. Install **kubectl**:  https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
3. Install **Helm**:  https://helm.sh/docs/intro/install/

### Installing

* Login to azure
```
az login --identity
```

* Get AKS Credential
```
az aks get-credentials --admin --name <cluster-name> --resource-group <resource-group-name>
```
 
* Create a new chart with the "simple-web" name
``` 
helm create simple-web
```

* Update the image repository & image tag in values.yaml file
```
image:
  repository: devopsinterviewacr.azurecr.io/simple-web
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "latest"
```

* Enable ingress and add the rewrite annotation

```
ingress:
  enabled: ture
  annotations: {
    kubernetes.io/ingress.class: "nginx",
    nginx.ingress.kubernetes.io/rewrite-target: /
  
  }
```
* Add path to hosts section
  
```
hosts:
    - host: chart-example.local
      paths:
      - path: /web-simple
        backend:
          serviceName: chart-example.local
          servicePort: 80
```

* Enable autoscaling 

```
autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80
```

* Install the charts
```
helm create simple-web /simple-web
```

### Jenkins installation
 - Install Jenkins : https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt2.
 - Create new project with name: simple-web-pipeline.
- Add new task {Execute shell} to Build section
- Add this command to task
  ```
  echo "Sign in with a managed identity"
  az login --identity
  az aks get-credentials --admin --name devops-interview-aks --resource-group devops-intreview-rg
  echo "Start Install simple-web"
  helm upgrade simple-web simple-web/
  ```
* Do some change on the repo: https://github.com/motim6026/simple-web
* The build will start and update the application.

## Authors
moti.malka25@gmail.com.

## License

This project is licensed under the moti malka License - see the LICENSE.md file for details
