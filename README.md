## Step 1
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
chown 1000:1000 /data/jenkins-volume
chmod 775 /data/jenkins-volume
```
