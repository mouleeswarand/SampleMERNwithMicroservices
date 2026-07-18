# Graded Project on Orchestration and Scaling

## End-to-End DevOps Pipeline for MERN Streaming Application

---

# Project Overview

This project demonstrates an end-to-end CI/CD pipeline for deploying a MERN Streaming Application using modern DevOps practices.

The application has been:

- Version controlled using Git and GitHub
- Containerized using Docker
- Images pushed to Amazon ECR Public
- Automated using Jenkins Pipeline
- Deployed to Amazon EKS (Kubernetes)

---

# Technology Stack

| Technology | Purpose |
|------------|---------|
| Git & GitHub | Source Code Management |
| Docker | Containerization |
| Amazon ECR Public | Docker Image Repository |
| Jenkins | Continuous Integration |
| Amazon EKS | Kubernetes Cluster |
| kubectl | Kubernetes Deployment |
| AWS CLI | AWS Service Management |
| Node.js | Backend Services |
| React | Frontend |

---

# Project Structure

```
StreamingApp
│
├── frontend
│   ├── Dockerfile
│   └── React Application
│
├── backend
│   ├── helloService
│   │      └── Dockerfile
│   │
│   └── profileService
│          └── Dockerfile
│
├── k8s
│   ├── frontend-deployment.yaml
│   ├── frontend-service.yaml
│   ├── hello-deployment.yaml
│   ├── hello-service.yaml
│   ├── profile-deployment.yaml
│   └── profile-service.yaml
│
├── Jenkinsfile
│
└── README.md
```

---

# Step 1 - Version Control using Git

## Repository

Forked the original repository (https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices) into my GitHub account.

GitHub Repository

```
https://github.com/mouleeswarand/SampleMERNwithMicroservices.git
```

## Sync Fork

To sync changes from upstream repository:

```bash
git remote add upstream https://github.com/UnpredictablePrashant/StreamingApp.git

git fetch upstream

git checkout main

git merge upstream/main

git push origin main
```

### Screenshot

<img width="1078" height="679" alt="image" src="https://github.com/user-attachments/assets/03f761ee-fc29-4d56-a016-55160234b65b" />


---

# Step 2 - Containerization

## Frontend Docker Image

Dockerfile created for React Frontend.

Build command

```bash
docker build -t frontend .
```

### Screenshot

**[Add Screenshot - Frontend Docker Build]**

---

## Hello Service Docker Image

Dockerfile created for Hello Service.

Build command

```bash
docker build -t hello .
```

### Screenshot

**[Add Screenshot - Hello Service Docker Build]**

---

## Profile Service Docker Image

Dockerfile created for Profile Service.

Build command

```bash
docker build -t profile .
```

### Screenshot

**[Add Screenshot - Profile Service Docker Build]**

---

## Verify Docker Images

```bash
docker images
```

### Screenshot

**[Add Screenshot - Docker Images Output]**

---

# Step 3 - Amazon ECR Public

Created separate repositories:

- frontend
- hello-service
- profile-service

Docker Login

```bash
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws
```

Tag Images

```bash
docker tag frontend public.ecr.aws/<repository>/frontend:latest

docker tag hello public.ecr.aws/<repository>/hello-service:latest

docker tag profile public.ecr.aws/<repository>/profile-service:latest
```

Push Images

```bash
docker push public.ecr.aws/<repository>/frontend:latest

docker push public.ecr.aws/<repository>/hello-service:latest

docker push public.ecr.aws/<repository>/profile-service:latest
```

### Screenshot

**[Amazon ECR Repositories]**

<img width="1450" height="248" alt="image" src="https://github.com/user-attachments/assets/86930369-1fe0-47ad-a31a-bbb0fd7dfa13" />


---

# Step 4 - AWS CLI Configuration

Installed AWS CLI.

Configured credentials using

```bash
aws configure
```

Verified configuration

```bash
aws sts get-caller-identity
```

### Screenshot

**[AWS CLI Configuration]**

<img width="647" height="142" alt="image" src="https://github.com/user-attachments/assets/fbb8a9ac-a60d-4991-b893-64e9101dc50a" />


---

# Step 5 - Jenkins Installation

Jenkins Server

```
https://jenkinsacademics.herovired.com/
```

Installed plugins

- Git
- Docker
- Pipeline
- AWS Credentials
- Docker Pipeline

Configured:

- AWS Credentials
- GitHub Repository
- Docker
- kubectl

### Screenshot

**[Jenkins Dashboard]**

<img width="1680" height="724" alt="image" src="https://github.com/user-attachments/assets/a57c27c1-59fc-4329-bee2-60e904aad881" />


### Screenshot

**[Jenkins Credentials]**

<img width="651" height="791" alt="image" src="https://github.com/user-attachments/assets/13bef8fa-8476-4288-a578-9b376ff6e242" />


---

# Step 6 - Jenkins Pipeline

Created Declarative Pipeline.

Pipeline Stages

- Checkout Code
- Build Frontend
- Build Backend Services
- List Docker Images
- Login to Amazon ECR
- Tag Docker Images
- Push Docker Images
- Configure kubeconfig
- Deploy to Amazon EKS
- Verify Rollout

Pipeline automatically triggers whenever code is pushed to GitHub.

### Screenshot

**[Jenkins Pipeline]**

<img width="812" height="843" alt="image" src="https://github.com/user-attachments/assets/92aae69d-000f-4075-8740-0f06c632cd83" />



### Screenshot

**[Successful Jenkins Build]**

<img width="1662" height="870" alt="image" src="https://github.com/user-attachments/assets/b8f30e22-3ab8-4837-81e7-f217edb5571e" />

### Jenkins file

```bash
pipeline {
    agent any

    stages {

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t frontend:latest .'
                }
            }
        }

        stage('Build Hello Service') {
            steps {
                dir('backend/helloService') {
                    sh 'docker build -t hello:latest .'
                }
            }
        }

        stage('Build Profile Service') {
            steps {
                dir('backend/profileService') {
                    sh 'docker build -t profile:latest .'
                }
            }
        }

        stage('List Images') {
            steps {
                sh 'docker images'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'mouli-aws-creds'
                ]]) {
                    sh '''
                    aws ecr-public get-login-password --region us-east-1 | \
                    docker login --username AWS --password-stdin public.ecr.aws
                    '''
                }
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag frontend:latest public.ecr.aws/b5a0w1i5/frontend:latest
                docker tag hello:latest public.ecr.aws/b5a0w1i5/hello-service:latest
                docker tag profile:latest public.ecr.aws/b5a0w1i5/profile-service:latest
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push public.ecr.aws/b5a0w1i5/frontend:latest
                docker push public.ecr.aws/b5a0w1i5/hello-service:latest
                docker push public.ecr.aws/b5a0w1i5/profile-service:latest
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'mouli-aws-creds'
                ]]) {
                    sh '''
                    aws eks update-kubeconfig \
                        --region ap-south-1 \
                        --name jenkins

                    kubectl get nodes

                    kubectl apply -f k8s/

                    kubectl rollout status deployment/frontend
                    kubectl rollout status deployment/hello-service
                    kubectl rollout status deployment/profile-service
                    '''
                }
            }
        }

    }
}
```

---

# Step 7 - Amazon EKS Cluster

Created Kubernetes Cluster using eksctl.

Verified Nodes

```bash
kubectl get nodes
```

Output

```
ip-192-168-19-43.ap-south-1.compute.internal    Ready    <none>   104m   v1.34.9-eks-8f14419
ip-192-168-79-103.ap-south-1.compute.internal   Ready    <none>   104m   v1.34.9-eks-8f14419
```

### Screenshot

**[kubectl get nodes]**

<img width="758" height="98" alt="image" src="https://github.com/user-attachments/assets/59a85852-df04-44c8-bb06-a5a4b184b38a" />


---

# Kubernetes Deployment

Applied Kubernetes manifests.

```bash
kubectl apply -f k8s/
```

Verified Deployments

```bash
kubectl get deployments
```

Verified Services

```bash
kubectl get svc
```

Verified Pods

```bash
kubectl get pods
```

### Screenshot

**[kubectl apply Output]**

<img width="665" height="157" alt="image" src="https://github.com/user-attachments/assets/7e218be2-fd29-472f-b164-3fcf62f9a85c" />


### Screenshot

**[kubectl get deployments]**

<img width="612" height="93" alt="image" src="https://github.com/user-attachments/assets/d8c5e1b3-0343-4b35-9375-f9b459d6ec1c" />


### Screenshot

**[kubectl get pods]**

<img width="566" height="78" alt="image" src="https://github.com/user-attachments/assets/e74e68e3-9db9-4879-ab8d-233a3c5e5d51" />


### Screenshot

**[Add Screenshot - kubectl get svc]**

<img width="753" height="193" alt="image" src="https://github.com/user-attachments/assets/3d91ac5f-0e81-4a52-a0dc-241df3744463" />


---

# Jenkins Deployment Output

The Jenkins pipeline successfully performed the following:

- Built Docker Images
- Logged into Amazon ECR Public
- Tagged Images
- Pushed Images
- Updated kubeconfig
- Connected to Amazon EKS
- Applied Kubernetes manifests
- Verified rollout status

### Console Output

**[Pipeline Success- console Output]**

```bash
Started by an SCM change
Obtained Jenkinsfile from git https://github.com/mouleeswarand/SampleMERNwithMicroservices.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins
 in /var/lib/jenkins/workspace/MERN-StreamingApp-Pipeline
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/MERN-StreamingApp-Pipeline/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/mouleeswarand/SampleMERNwithMicroservices.git # timeout=10
Fetching upstream changes from https://github.com/mouleeswarand/SampleMERNwithMicroservices.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/mouleeswarand/SampleMERNwithMicroservices.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse origin/main^{commit} # timeout=10
Checking out Revision b0c6323708ffcf3050c7e1f0acb6a08ffa80568a (origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f b0c6323708ffcf3050c7e1f0acb6a08ffa80568a # timeout=10
Commit message: "Add README with setup instructions for services"
 > git rev-list --no-walk ece8ffe0e4b52c220d76129353407af010b5afa1 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Build Frontend)
[Pipeline] dir
Running in /var/lib/jenkins/workspace/MERN-StreamingApp-Pipeline/frontend
[Pipeline] {
[Pipeline] sh
+ docker build -t frontend:latest .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  753.7kB

Step 1/9 : FROM node:20-alpine AS build
 ---> fb4cd12c85ee
Step 2/9 : WORKDIR /app
 ---> Using cache
 ---> 3d1911d4e94e
Step 3/9 : COPY . .
 ---> 15b1b7fff37d
Step 4/9 : RUN npm install
 ---> Running in 5cb29d7bfe2b
[91mnpm warn deprecated w3c-hr-time@1.0.2: Use your platform's native performance.now() and performance.timeOrigin.
[0m[91mnpm warn deprecated stable@0.1.8: Modern JS already guarantees Array#sort() is a stable sort, so this library is deprecated. See the compatibility table on MDN: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#browser_compatibility
[0m[91mnpm warn deprecated sourcemap-codec@1.4.8: Please use @jridgewell/sourcemap-codec instead
[0m[91mnpm warn deprecated workbox-cacheable-response@6.6.0: workbox-background-sync@6.6.0
[0m[91mnpm warn deprecated rollup-plugin-terser@7.0.2: This package has been deprecated and is no longer maintained. Please use @rollup/plugin-terser
[0m[91mnpm warn deprecated domexception@2.0.1: Use your platform's native DOMException instead
[0m[91mnpm warn deprecated abab@2.0.6: Use your platform's native atob() and btoa() methods instead
[0m[91mnpm warn deprecated svgo@1.3.2: This SVGO version is no longer supported. Upgrade to v2.x.x.
[0m[91mnpm warn deprecated @babel/plugin-proposal-private-methods@7.18.6: This proposal has been merged to the ECMAScript standard and thus this plugin is no longer maintained. Please use @babel/plugin-transform-private-methods instead.
[0m[91mnpm warn deprecated @babel/plugin-proposal-numeric-separator@7.18.6: This proposal has been merged to the ECMAScript standard and thus this plugin is no longer maintained. Please use @babel/plugin-transform-numeric-separator instead.
[0m[91mnpm warn deprecated @babel/plugin-proposal-class-properties@7.18.6: This proposal has been merged to the ECMAScript standard and thus this plugin is no longer maintained. Please use @babel/plugin-transform-class-properties instead.
[0m[91mnpm warn deprecated @babel/plugin-proposal-nullish-coalescing-operator@7.18.6: This proposal has been merged to the ECMAScript standard and thus this plugin is no longer maintained. Please use @babel/plugin-transform-nullish-coalescing-operator instead.
[0m[91mnpm warn deprecated @babel/plugin-proposal-optional-chaining@7.21.0: This proposal has been merged to the ECMAScript standard and thus this plugin is no longer maintained. Please use @babel/plugin-transform-optional-chaining instead.
[0m
added 1534 packages, and audited 1535 packages in 32s

246 packages are looking for funding
  run `npm fund` for details

67 vulnerabilities (15 low, 20 moderate, 29 high, 3 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
[91mnpm notice
npm notice New major version of npm available! 10.8.2 -> 12.0.1
npm notice Changelog: https://github.com/npm/cli/releases/tag/v12.0.1
npm notice To update run: npm install -g npm@12.0.1
npm notice
[0m ---> Removed intermediate container 5cb29d7bfe2b
 ---> 080f1f09b555
Step 5/9 : RUN npm run build
 ---> Running in e440bd382908

> frontend@0.1.0 build
> react-scripts build

Creating an optimized production build...
[91mBrowserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
[0m[91m[0;33mOne of your dependencies, babel-preset-react-app, is importing the
"@babel/plugin-proposal-private-property-in-object" package without
declaring it in its dependencies. This is currently working because
"@babel/plugin-proposal-private-property-in-object" is already in your
node_modules folder for unrelated reasons, but it [1mmay break at any time[0;33m.

babel-preset-react-app is part of the create-react-app project, [1mwhich
is not maintianed anymore[0;33m. It is thus unlikely that this bug will
ever be fixed. Add "@babel/plugin-proposal-private-property-in-object" to
your devDependencies to work around this error. This will make this message
go away.[0m
  
[0m[91mBrowserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
[0mCompiled successfully.

File sizes after gzip:

  57.68 kB  build/static/js/main.7c7a6051.js
  1.77 kB   build/static/js/787.9e9f4ffd.chunk.js
  513 B     build/static/css/main.f855e6bc.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment

 ---> Removed intermediate container e440bd382908
 ---> 91f8e0ab3c8d
Step 6/9 : FROM nginx:alpine
 ---> 7068961d45b0
Step 7/9 : COPY --from=build /app/build /usr/share/nginx/html
 ---> Using cache
 ---> 61d4a150eea7
Step 8/9 : EXPOSE 80
 ---> Using cache
 ---> f007afdfc8c4
Step 9/9 : CMD ["nginx", "-g", "daemon off;"]
 ---> Using cache
 ---> 31df21fa466b
Successfully built 31df21fa466b
Successfully tagged frontend:latest
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build Hello Service)
[Pipeline] dir
Running in /var/lib/jenkins/workspace/MERN-StreamingApp-Pipeline/backend/helloService
[Pipeline] {
[Pipeline] sh
+ docker build -t hello:latest .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  43.01kB

Step 1/7 : FROM node:26-alpine3.23
 ---> 0473b6671ff2
Step 2/7 : WORKDIR /app
 ---> Using cache
 ---> 41a3a9141727
Step 3/7 : COPY . .
 ---> Using cache
 ---> 34a41aa26f96
Step 4/7 : RUN npm install
 ---> Using cache
 ---> 811e77e6965b
Step 5/7 : ENV PORT=3001
 ---> Using cache
 ---> 5ee93624d292
Step 6/7 : EXPOSE 3001
 ---> Using cache
 ---> 18feee469b83
Step 7/7 : CMD [ "index.js", "node" ]
 ---> Using cache
 ---> fe580801de21
Successfully built fe580801de21
Successfully tagged hello:latest
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build Profile Service)
[Pipeline] dir
Running in /var/lib/jenkins/workspace/MERN-StreamingApp-Pipeline/backend/profileService
[Pipeline] {
[Pipeline] sh
+ docker build -t profile:latest .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  53.25kB

Step 1/8 : FROM node:26-alpine3.23
 ---> 0473b6671ff2
Step 2/8 : WORKDIR /app
 ---> Using cache
 ---> 41a3a9141727
Step 3/8 : COPY . .
 ---> Using cache
 ---> 5a6ee40b63f3
Step 4/8 : RUN npm install
 ---> Using cache
 ---> c439ef5d139f
Step 5/8 : ENV PORT=3002
 ---> Using cache
 ---> b60b7ae16221
Step 6/8 : ENV MONGO_URL="mongodb+srv://dmouleeswaran_db_user:3Dn8tFezX09qvY6t@cluster0.vsknysx.mongodb.net/profile"
 ---> Using cache
 ---> 14d0362e5e2a
Step 7/8 : EXPOSE 3002
 ---> Using cache
 ---> 60a26e04c6bb
Step 8/8 : CMD ["index.js", "node"]
 ---> Using cache
 ---> 7d3018c9ea8a
Successfully built 7d3018c9ea8a
Successfully tagged profile:latest
[Pipeline] }
[Pipeline] // dir
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (List Images)
[Pipeline] sh
+ docker images
WARNING: This output is designed for human readability. For machine-readable output, please use --format.
IMAGE                                                                     ID             DISK USAGE   CONTENT SIZE   EXTRA
258274811560.dkr.ecr.ap-south-1.amazonaws.com/admin-service:latest        30ff9fc9e403        271MB         59.5MB        
258274811560.dkr.ecr.ap-south-1.amazonaws.com/auth-service:latest         f04c8076a077        241MB         56.3MB        
258274811560.dkr.ecr.ap-south-1.amazonaws.com/chat-service:latest         a181cf7e6091        230MB         54.8MB        
258274811560.dkr.ecr.ap-south-1.amazonaws.com/streaming-frontend:latest   4ff74859e772       78.2MB         21.9MB        
258274811560.dkr.ecr.ap-south-1.amazonaws.com/streaming-service:latest    dc0c36f04dff        271MB         59.2MB        
admin-service:latest                                                      30ff9fc9e403        271MB         59.5MB        
auth-service:latest                                                       f04c8076a077        241MB         56.3MB        
chat-service:latest                                                       a181cf7e6091        230MB         54.8MB        
frontend:latest                                                           31df21fa466b       93.7MB         26.3MB        
hello:latest                                                              fe580801de21        267MB         65.9MB        
nginx:1.27-alpine                                                         65645c7bb6a0       74.5MB         21.9MB        
nginx:alpine                                                              7068961d45b0       93.6MB           27MB        
node:18-alpine                                                            8d6421d663b4        181MB         45.3MB        
node:20-alpine                                                            fb4cd12c85ee        194MB         48.8MB        
node:26-alpine3.23                                                        0473b6671ff2        250MB         63.1MB        
profile:latest                                                            7d3018c9ea8a        294MB         71.8MB        
public.ecr.aws/b5a0w1i5/frontend:latest                                   31df21fa466b       93.7MB         26.3MB        
public.ecr.aws/b5a0w1i5/hello-service:latest                              fe580801de21        267MB         65.9MB        
public.ecr.aws/b5a0w1i5/mernservices:frontend                             31df21fa466b       93.7MB         26.3MB        
public.ecr.aws/b5a0w1i5/mernservices:hello                                fe580801de21        267MB         65.9MB        
public.ecr.aws/b5a0w1i5/mernservices:profile                              7d3018c9ea8a        294MB         71.8MB        
public.ecr.aws/b5a0w1i5/profile-service:latest                            7d3018c9ea8a        294MB         71.8MB        
python:3.12                                                               a9e4190f0272       1.62GB          429MB   U    
streaming-frontend:latest                                                 4ff74859e772       78.2MB         21.9MB        
streaming-service:latest                                                  dc0c36f04dff        271MB         59.2MB        
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Login to ECR)
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ aws ecr-public get-login-password --region us-east-1
+ docker login --username AWS --password-stdin public.ecr.aws

WARNING! Your credentials are stored unencrypted in '/var/lib/jenkins/.docker/config.json'.
Configure a credential helper to remove this warning. See
https://docs.docker.com/go/credential-store/

Login Succeeded
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Tag Images)
[Pipeline] sh
+ docker tag frontend:latest public.ecr.aws/b5a0w1i5/frontend:latest
+ docker tag hello:latest public.ecr.aws/b5a0w1i5/hello-service:latest
+ docker tag profile:latest public.ecr.aws/b5a0w1i5/profile-service:latest
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Push Images)
[Pipeline] sh
+ docker push public.ecr.aws/b5a0w1i5/frontend:latest
The push refers to repository [public.ecr.aws/b5a0w1i5/frontend]
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46f977ee452f: Layer already exists
3cd534fe98c6: Waiting
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
390dc935348d: Layer already exists
55afa1ecc21d: Waiting
d0008c891db4: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
3cd534fe98c6: Waiting
46519e7231d2: Waiting
3cd534fe98c6: Layer already exists
46519e7231d2: Waiting
55afa1ecc21d: Waiting
d0008c891db4: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
46519e7231d2: Layer already exists
55afa1ecc21d: Waiting
d0008c891db4: Waiting
1223f016b4e4: Waiting
62bec68d7c31: Waiting
78944344b200: Waiting
1223f016b4e4: Layer already exists
62bec68d7c31: Layer already exists
78944344b200: Layer already exists
55afa1ecc21d: Layer already exists
d0008c891db4: Layer already exists
latest: digest: sha256:31df21fa466bc01b9c8093783cfc4c77aa06f2a9ba27b2676b9158398d47b930 size: 2264
+ docker push public.ecr.aws/b5a0w1i5/hello-service:latest
The push refers to repository [public.ecr.aws/b5a0w1i5/hello-service]
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
805e4291d2a1: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
25db4ba283d7: Layer already exists
805e4291d2a1: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
805e4291d2a1: Layer already exists
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Waiting
abe552397b13: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
9c53a4484d07: Layer already exists
abe552397b13: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Waiting
abe552397b13: Waiting
e6f31ffc071e: Waiting
17d6cbd80642: Layer already exists
abe552397b13: Layer already exists
e6f31ffc071e: Layer already exists
latest: digest: sha256:fe580801de21ded404eaf366fd7e9321ae7ed8308b32d082d068993cbe3904d3 size: 1617
+ docker push public.ecr.aws/b5a0w1i5/profile-service:latest
The push refers to repository [public.ecr.aws/b5a0w1i5/profile-service]
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
25db4ba283d7: Waiting
ed868efae640: Layer already exists
9c53a4484d07: Waiting
25db4ba283d7: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Waiting
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
abe552397b13: Layer already exists
25db4ba283d7: Layer already exists
9c53a4484d07: Waiting
e6f31ffc071e: Waiting
d3404ca02858: Waiting
9c53a4484d07: Layer already exists
e6f31ffc071e: Layer already exists
d3404ca02858: Layer already exists
latest: digest: sha256:7d3018c9ea8a6530d734adc972c97935a8ca6df9261767f6b1f127074cea68fa size: 1617
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy to EKS)
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ aws eks update-kubeconfig --region ap-south-1 --name jenkins
Updated context arn:aws:eks:ap-south-1:562437414725:cluster/jenkins in /var/lib/jenkins/.kube/config
+ kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-19-43.ap-south-1.compute.internal    Ready    <none>   81m   v1.34.9-eks-8f14419
ip-192-168-79-103.ap-south-1.compute.internal   Ready    <none>   81m   v1.34.9-eks-8f14419
+ kubectl apply -f k8s/
deployment.apps/frontend unchanged
service/frontend-service unchanged
deployment.apps/hello-service unchanged
service/hello-service unchanged
deployment.apps/profile-service unchanged
service/profile-service unchanged
+ kubectl rollout status deployment/frontend
deployment "frontend" successfully rolled out
+ kubectl rollout status deployment/hello-service
deployment "hello-service" successfully rolled out
+ kubectl rollout status deployment/profile-service
deployment "profile-service" successfully rolled out
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS

```
<img width="1671" height="870" alt="image" src="https://github.com/user-attachments/assets/9947b45a-dc03-443b-a97c-9bdd7a3ff0a8" />


---

# CI/CD Workflow

```
GitHub
   │
   ▼
Jenkins Pipeline
   │
   ▼
Docker Build
   │
   ▼
Amazon ECR Public
   │
   ▼
Amazon EKS
   │
   ▼
Running Kubernetes Pods
```


## Final URL (```http://a0be8428cd48f4dfbbd0ed913b0bd411-1446058346.ap-south-1.elb.amazonaws.com/```)
<img width="1679" height="402" alt="image" src="https://github.com/user-attachments/assets/1d3eab11-9d9e-481e-8145-1f99360af1ed" />

## Database

<img width="775" height="511" alt="image" src="https://github.com/user-attachments/assets/65ba6219-a4df-4cb4-9b4e-43ef0ce00957" />

## K8s - logs

<img width="732" height="41" alt="image" src="https://github.com/user-attachments/assets/4af12eed-3dd8-4e77-9331-e472ea292da6" />

<img width="765" height="174" alt="image" src="https://github.com/user-attachments/assets/cbb1c8b6-3a3a-425d-8275-1fdd7a56ef47" />




### Screenshot

**[Add Screenshot - Architecture Diagram]**

---

# Current Status

| Task | Status |
|-------|--------|
| GitHub Repository | ✅ Completed |
| Docker Containerization | ✅ Completed |
| Amazon ECR Push | ✅ Completed |
| AWS CLI | ✅ Completed |
| Jenkins Pipeline | ✅ Completed |
| Automatic GitHub Trigger | ✅ Completed |
| Amazon EKS Cluster | ✅ Completed |
| Kubernetes Deployment | ✅ Completed |
| Rollout Verification | ✅ Completed |
| Helm Deployment | ⏳ Pending |
| CloudWatch Monitoring | ⏳ Pending |
| CloudWatch Logging | ⏳ Pending |

---

# Future Enhancements

- Deploy using Helm Charts
- Configure Amazon CloudWatch Monitoring
- Configure CloudWatch Container Logs
- Horizontal Pod Autoscaler
- Ingress Controller
- SSL/TLS Support

---

# Author

**Mouleeswaran Duraisamy**

PPMACD Batch-16A

Graded Project on Orchestration and Scaling
