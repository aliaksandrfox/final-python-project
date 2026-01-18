# Final DevOps Course Project

**Student:** 
•	Aliaksandr Lisouski
 alecsli@ac.sce.ac.il


•	Yavgeni Karavkin
 ivgenkr@ac.sce.ac.il


**GitHub Repo:** https://github.com/aliaksandrfox/final-python-project  
**Public App URL:** http://128.251.225.63:5000/api/doc

## Part A – Containerization

We chose to write a **custom Dockerfile** for the repository:  
https://github.com/lidorg-dev/final-python

### Steps:
1. Analyzed the app (`app.py`, `requirements.txt`, port `5000`).
2. Created a minimal `Dockerfile` based on `python:3.8-slim`.<br>
3. Built and ran the container locally.<br><br>
<img width="2028" height="547" alt="image" src="https://github.com/user-attachments/assets/1e653cec-3466-4a2a-b2af-2f0678abefa1" />
<br><br><br>
<img width="2005" height="487" alt="image" src="https://github.com/user-attachments/assets/b80899bf-2543-4a75-8761-13a90fa0264d" />
<br><br><br>
4. Verified the Swagger UI at `http://localhost:5000/api/doc`.


### Screenshots
<img width="1383" height="1207" alt="image" src="https://github.com/user-attachments/assets/8a770e4a-4412-4e66-98d0-c7a331da960a" />
* Repository showing the added `Dockerfile`. <br><br><br>


<img width="1085" height="682" alt="image" src="https://github.com/user-attachments/assets/a2a189fd-d027-4ec5-a2fb-1285e45c466f" />

* `Dockerfile` used to build the image.<br><br><br>

* Explanation:

-Uses a lightweight Python 3.8 base image.

-Installs dependencies without caching to reduce image size.

-Copies the entire application into the container.

-Exposes port 5000 for external access.

-Sets the entry point to run app.py.<br><br><br>

### Local Build and Run
Executed the following commands:
`docker build -t final-python-app .`

`docker run -d -p 5000:5000 --name project-python-app final-python-app` <br><br>

### Opened browser at:
`http://localhost:5000/api/doc`


<img width="2819" height="1525" alt="image" src="https://github.com/user-attachments/assets/3d7025e2-2a85-4641-9f96-626addd69794" />
<br><br><br>

---

## Part B – CI/CD with GitHub Actions & Docker Hub 

### Docker Hub Setup
Created account at `hub.docker.com`
Created repository: `aliaksandrfox/devops_project`

### GitHub Secrets Configuration
Added the following secrets in GitHub (Settings → Secrets and variables → Actions):<br><br>
<img width="1962" height="443" alt="image" src="https://github.com/user-attachments/assets/44b380ef-7711-4178-ac31-76387ff7c1f8" /><br><br>


`DOCKERHUB_USERNAME` = aliaksandrfox<br>

`DOCKERHUB_TOKEN` = personal access token (PAT) from Docker Hub

### Workflow Creation
File: `.github/workflows/project_workflow.yml`


Using the same code from Part A, I configured a GitHub Actions workflow to:
- Build the Docker image on every push to `main`.
- Push it to my Docker Hub repository: [`aliaksandrfox/devops_project`](https://hub.docker.com/r/aliaksandrfox/devops_project).
  <img width="1383" height="1277" alt="image" src="https://github.com/user-attachments/assets/1e5d19be-f400-47d0-b8e7-eaecaba47f41" /><br><br><br>


### Workflow Features:
- Uses `docker/login-action` and `docker/build-push-action`.
- Tags image as `latest` and with GitHub run number.
- Secrets stored securely in GitHub (`DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`).

### Screenshots

<img width="1881" height="1630" alt="image" src="https://github.com/user-attachments/assets/d4737131-2db9-41e4-88a3-e5fb9dc86f27" />

* `.github/workflows/project_workflow.yml` in repository. <br><br><br>

<img width="2822" height="1139" alt="image" src="https://github.com/user-attachments/assets/509af8d6-1e1d-45ef-b5cb-ee2a180c608a" />

* Workflow completed successfully in under 1 minute. <br><br><br>


<img width="2775" height="904" alt="image" src="https://github.com/user-attachments/assets/db616f2a-da18-4ea5-93fc-52f527618d40" /><br><br>
<img width="1316" height="1088" alt="image" src="https://github.com/user-attachments/assets/a0e19a4e-c12c-4997-ba13-fe05ba3d4f6a" />

* Image visible in Docker Hub with tags `latest` and `2`. <br><br><br>

---

## Part C – Deployment to Azure VM 

I selected **Option 1**: Deploy to Azure Virtual Machine via GitHub Actions.

### Setup:
- Created Ubuntu 22.04 VM in Azure (Standard D2s v3, West Europe).
- Opened port `5000` in Network Security Group (NSG).
- Installed Docker on the VM manually.
- Extended GitHub workflow to deploy via SSH using `appleboy/ssh-action`.

### Deployment Script:
- Stops and removes any existing container.
- Pulls latest image from Docker Hub.
- Runs new container on port `5000`.

### Screenshots
<img width="1560" height="384" alt="image" src="https://github.com/user-attachments/assets/08d13691-535c-4f89-b976-86008c3e13a7" /><br><br>


<img width="1762" height="921" alt="image" src="https://github.com/user-attachments/assets/f344d341-b02a-42df-ad6e-a0cdcb285261" /><br><br>
 
*YAML file showing the `deploy-to-azure` job.* <br><br>
<img width="2816" height="703" alt="image" src="https://github.com/user-attachments/assets/a0166ab8-3ef5-4ebc-8a85-86cb0ad37276" /> <br><br>

<img width="2814" height="550" alt="image" src="https://github.com/user-attachments/assets/a2b3c5c5-78af-496a-b297-4037aa3836a3" />
 
*Both `build-and-push` and `deploy-to-azure` jobs succeeded.*<br><br>

<img width="2242" height="1556" alt="image" src="https://github.com/user-attachments/assets/2477c118-16a9-4219-b1fb-df3abc79ee95" />

*VM details: OS = Ubuntu 22.04, Size = D2s v3, Public IP = 128.251.225.63.*<br><br>

<img width="2091" height="1142" alt="image" src="https://github.com/user-attachments/assets/5b805002-4768-4e66-9156-6f063d674bae" />
`http://128.251.225.63:5000/api/doc`





