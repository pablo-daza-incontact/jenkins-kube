# Use Docker installed in WSL 2 or change the paths of volume

## Step 1 (Simple deployment internal)
Create C:\jenkins folder to persist the data
```bash
kubectl create namespace jenkins
kubectl apply -f jenkins-volume-manual.yaml
kubectl apply -f jenkins-volume-claim.yaml
kubectl apply -f jenkins-sa.yaml
kubectl create -f jenkins.yaml --namespace jenkins
```

Use the following commands to see the password key
kubectl get pods -n jenkins
kubectl logs jenkins-xxxxxxxxx -n jenkins

## Step 1 (Advanced not necessary if you followed the simple deployment)
#### Install Helm in the folder you want
```shell
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
Execute the following command to add Jenkins charts
```shell
helm repo add jenkinsci https://charts.jenkins.io
helm repo update
helm search repo jenkinsci
```

## Step 2
#### Create volume and Secure Association

##### Execute the following command 
```shell
kubectl create namespace jenkins
kubectl apply -f jenkins-volume.yaml
kubectl apply -f jenkins-sa.yaml
```
## Step 3
#### Startup Jenkins cluster
##### Execute the following commands
```shell
helm install jenkins -n jenkins -f jenkins-values.yaml jenkinsci/jenkins
```
##### Use to verify
```shell
kubectl logs -n jenkins jenkins-0 -c init
```
## Troubleshooting
The volume for some reason does not have the correct permissions 
And set
```shell
chown 1000:1000 /mnt/c/jenkins
chmod 775 /mnt/c/jenkins
```


	• if the admin password was not set in the values file use the following commands to retrieve it
```shell
jsonpath="{.data.jenkins-admin-password}"
secret=$(kubectl get secret -n jenkins jenkins -o jsonpath=$jsonpath)
echo $(echo $secret | base64 --decode)
```

	• Get the Jenkins URL to visit by running these commands in the same shell:
```shell
jsonpath="{.spec.ports[0].nodePort}"
NODE_PORT=$(kubectl get -n jenkins -o jsonpath=$jsonpath services jenkins)
jsonpath="{.items[0].status.addresses[0].address}"
NODE_IP=$(kubectl get nodes -n jenkins -o jsonpath=$jsonpath)
echo http://$NODE_IP:$NODE_PORT/login
```

