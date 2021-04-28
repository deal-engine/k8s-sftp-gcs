# GCS SFTP Server
SFTP Server designed to store data in Google Cloud Storage (GCS) Buckets

This is based upon [mikeghen/kubernetes-gcs-sftp](https://github.com/mikeghen/kubernetes-gcs-sftp) project.

# Setup Instructions

## Deployment
To deploy to GKE follow these steps:

### 1. Create Service Account JSON key file for your bucket

https://cloud.google.com/iam/docs/creating-managing-service-account-keys

### 2. Generate SSH private key
```ssh-keygen -t rsa -b 4096 -f ssh_host_rsa_key -N ''```

### 3. Setup Secrets

You can run these commands to put these files on the cluster as secrets:
```
kubectl create secret generic sftp-gcloud-key --from-file=gcloud-key.json
kubectl create secret generic ssh-secret-key --from-file=ssh_host_rsa_key
```

### 4. Create `users.yaml` file:
for example:
```
users:
  - user: my-user-1
    password: my-password-1
    bucket: my_gcs_bucket
  - user: my-user-2
    password: my-password-2
    bucket: my_gcs_bucket
    onlyDir: dir-for-user2
```


### 4. Deploy the SFTP server to Kubernetes:
```
helm upgrade -i k8s-sftp-gcs ./helm -f users.yaml
```
### 5. Get the test IP and port:
```kubectl get svc k8s-sftp-gcs```

This will give you EXTERNAL-IP and port.

:information_source: We use Port 2022 for SFTP.

### 6. Confirm you can SFTP using the usernames and password you setup in `etc/sftp*` with `sftp` utility:
```
$ sftp -P 2022 username@1.2.3.4
username@1.2.3.4's password:
sftp> pwd
Remote working directory: /ftp
```

