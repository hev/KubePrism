# KubePrism
Combines Aerospike Vector Search and a [data pipeline for your google photos](https://github.com/dtylman/gitmoo-goog). 
Use these config files and instructions to deploy and host your own hosted
photo search solution.

### Step 1 - Install Aerospike + Aerospike Vector Search (AVS)
Follow the instructions on the Aerospike Vector Search documentation.

> [!NOTE]
> Aerospike Vector Search (AVS) requires a feature key. [Request one](https://aerospike.com/docs/vector?utm_medium=web&utm_source=aerospike-github).

### Step 2 - Create a Persistent Volume Claim (PVC)
Next we'll need to share a persistent volume claim that is shared by
the data pipeline and the image search application. This is done with 
the pvc.yaml file. You can edit the file to adjust your storage needs
for your photos. 

```
kubectl apply -f config/pvc.yaml
```
### Step 3 - Get your Google Photos Credentials
Next you need to get credentials for the data pipeline to read your
Google Photos. You do this by going through the following steps.

* Enable the API for ["Google Photos Picker"](https://console.cloud.google.com/apis/library/photospicker.googleapis.com?project=photos-427213)
* Go to Credentials and create a new OAuth Client ID credential and choose desktop app
* Copy the clientID and client secret values into `config/google-photos-credentials.yaml` and download the credentials.json file.
* Clone the repo for the data pipeline `git clone https://github.com/dtylman/gitmoo-goog`
* Copy the `credentials.json` file into this directory and run the binary
* After clicking through this will create a `token.json` file. Insert this content into the config/google-photos-credentials.yaml file
* Apply the config map for your credentials.

```
kubectl apply -f config/google-photo-credentials.yaml
```

> [!WARNING]
> You may want to consider using a secret. Do not share your Google Photo credentials. 

### Step 4 - Deploy the Photo Pipeline
Once you have configured your credentials and created your PVC you are ready to
deploy the Google Photo data pipeline. You can do that by deploying the
gitmogoog application.

```
kubectl apply -f config/gitmoo-goog-deployment.yaml
```

### Step 5 - Deploy the prism photo search app
The final setp is to deploy the prism photo search application which will allow
you to search you photos using natural language. You do this by appying the following
deployment yaml.

```
kubectl apply -f config/prism-search-deployment.yaml
```

