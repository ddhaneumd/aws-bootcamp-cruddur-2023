# Week 1 — App Containerization


## VSCode Docker Extension

Docker for VSCode makes it easy to work with Docker

https://code.visualstudio.com/docs/containers/overview

> Gitpod is preinstalled with this extension

## Containerize Backend

## Basics
The /backend-flask is available on both HOST Operating system and the Operating system inside the container.
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/ccade642-d667-4f57-99ce-e99ca57ab273)

Hence, we will write a Dockerfile in both the frontend and backend to give commands to specify which work directory Docker should check once we run it. This Dockerfile is specified in the "Add Dockerfile" section in this document.

### Run Python

Run the Python command as shown below
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/887a6fbd-2515-43b6-abaa-8402f8ecd252)

Check the ports
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/5b98c33a-b576-46a0-93d7-5fae958772de)


Whoops!, we got an error
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/42036268-8369-4e32-824e-5f545538f60a)


We Got this error because we have not set Env Vars named FRONTEND_URL and BACKEND_URL mentioned in app.py
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/e0f32a60-a4aa-4068-a664-06ef0ded7016)


Set the Env Vars
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/abae64a9-8568-4af5-b630-8a5aac1aeebe)


Rerun the app, and unlock the port by clicking on the padlock icon
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/01f58fd4-a1c8-4d70-b992-015a66433890)

append /api/activities/home at the end of the URL

https://4567-ddhaneumd-awsbootcampcr-t0n397ust2q.ws-us104.gitpod.io/api/activities/home 
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/a26ac307-5bcb-43f2-8891-1a318994eae3)


Here are the simple and consolidated steps:

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```
Some key points to keep in check:

- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to `/api/activities/home`
- you should get back json

### Add Dockerfile

Create a file here: `backend-flask/Dockerfile`

```dockerfile
FROM python:3.10-slim-buster

# make a new folder inside container
WORKDIR /backend-flask

#going from outside container to inside of the container
# contains libraries which we want to install to run the application
COPY requirements.txt requirements.txt

#install python libraries used for the app
RUN pip3 install -r requirements.txt

#going from outside container to inside of the container
# here "." means everything in the current directory. That is first "." means
# everything in /backend-flask (outside container) and second "." means everything in /backend-flask (inside container)
COPY . .

#set Environment Variables (Env Vars)
# inside container and will remain set when the container is running
ENV FLASK_ENV=development

EXPOSE ${PORT}
#command to run the app
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

### Build Container

### Difference Between Virtualization and Containarization
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/4167350b-1279-46fc-ab87-ce74972f5f0b)


First, unset the previous env vars
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/561a967e-8218-4e70-8c71-0120a42cf3b8)

Here the -t is tag, the default tag is “latest”

```sh
docker build -t  backend-flask ./backend-flask
```

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/331a9b0a-90d7-4aed-beed-9b8e714869fb)

### Run Container

Before running it, set environment variables otherwise it will throw 404 (remember the previous example)
```sh
export FRONTEND_URL="*"
export BACKEND_URL="*"
```
Check the docker images
```
docker images
```
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/823e6b3a-92df-4f6e-bf98-7ce881c9971c)

Check the list of running containers by using the following command
```
docker ps
```
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/33e0fd94-00d0-4d9e-b88b-f02bd88d5ce0)


Run 
```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask

```

Run in background
```sh
docker container run --rm -p 4567:4567 -d backend-flask
```

Return the container id into an Env Vat
```sh
CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)
```

> docker container run is idiomatic, docker run is legacy syntax but is commonly used.

### Get Container Images or Running Container Ids

```
docker ps
docker images
```


### Send Curl to Test Server

```sh
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"
```

### Check Container Logs

```sh
docker logs CONTAINER_ID -f
docker logs backend-flask -f
docker logs $CONTAINER_ID -f
```

###  Debugging  adjacent containers with other containers

```sh
docker run --rm -it curlimages/curl "-X GET http://localhost:4567/api/activities/home -H \"Accept: application/json\" -H \"Content-Type: application/json\""
```

busybosy is often used for debugging since it install a bunch of thing

```sh
docker run --rm -it busybosy
```

### Gain Access to a Container

```sh
docker exec CONTAINER_ID -it /bin/bash
```

> You can just right click a container and see logs in VSCode with Docker extension

### Delete an Image

```sh
docker image rm backend-flask --force
```

> docker rmi backend-flask is the legacy syntax, you might see this is old docker tutorials and articles.

> There are some cases where you need to use the --force

### Overriding Ports

```sh
FLASK_ENV=production PORT=8080 docker run -p 4567:4567 -it backend-flask
```

> Look at Dockerfile to see how ${PORT} is interpolated

## Containerize Frontend

## Run NPM Install

We have to run NPM Install before building the container since it needs to copy the contents of node_modules

```
cd frontend-react-js
npm i
```
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/8eb55ad3-7799-4e0b-88d8-e8bcda6971e4)

### Create Docker File

Create a file here: `frontend-react-js/Dockerfile`

```dockerfile
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

### Build Container

```sh
docker build -t frontend-react-js ./frontend-react-js
```

### Run Container

```sh
docker run -p 3000:3000 -d frontend-react-js
```

## Multiple Containers

To run multiple containers at the same time, (that is our backend and frontend containers), we use the docker compose utility, make the following YAML file and click on compose up.

### Create a docker-compose file

Create `docker-compose.yml` at the root of your project.

```yaml
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/a455d262-c00d-4318-8637-7ec0a40ee0ff)

We can see the output as a Backend and Frontend container is created

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/9a0e0195-3c7d-47b9-bf99-687608b76bfa)

Now check the link running on port 3000

https://3000-ddhaneumd-awsbootcampcr-t0n397ust2q.ws-us104.gitpod.io/

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/02d8d03e-fd72-433f-8673-d10433de7dc7)


## Adding DynamoDB Local and Postgres

We are going to use Postgres and DynamoDB local in future labs
We can bring them in as containers and reference them externally

Lets integrate the following into our existing docker compose file:

### Postgres

```yaml
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

To install the postgres client into Gitpod

```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

### DynamoDB Local

```yaml
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

Example of using DynamoDB local
https://github.com/100DaysOfCloud/challenge-dynamodb-local

## Volumes

directory volume mapping

```yaml
volumes: 
- "./docker/dynamodb:/home/dynamodblocal/data"
```

named volume mapping

```yaml
volumes: 
  - db:/var/lib/postgresql/data

volumes:
  db:
    driver: local
```

## Spending Considerations:

Cloud Development Environments
1. GitPod
   - for an active workspace, go to user settings and check billing tab.
   - Up to 50 hours of usage/month, Standard : 4 cores, 8GB RAM & 30GB storage
   - Do not spinning up multiple environments simultaniously
   - https://gitpod.io/workspaces

2. GitHub Codespaces
   - Simple and similar to GitPod, it will be inactive after we close the tab.
   - Up to 60 hours of usage/month with : 2 cores 4 GB RAM and 15 GB of storage
   - Up to 30 hours of usage/month with : 4 cores 8 GB RAM and 15 GB of storage
   - https://github.com/codespaces
  	
3. AWS Cloud9
   - can be used with an AWS free tier account with t2.micro instance
   - Avoid using Cloud9 in case of free tier instance in use for other purpose
   - Try to avoid setting up AWS CloudTrail to remain under the free tier. It Logs all API requests, by default for 90 days.


## Best Practices for Securing Docker Containers

What is Container Security?
Container Security refers to the practice of safeguarding your applications hosted on computing services like Containers. Examples of such applications include Single Page Applications (SPAs), Microservices, and APIs.

Components of Container Security
1. Docker & Host Configuration
2. Image Security
3. Management of Secrets
4. Application Security
5. Data Security
6. Container Monitoring
7. Compliance Framework

Security Best Practices
- Keep Host & Docker Up-to-Date with the Latest Security Patches.
- Ensure Docker daemon & containers run in non-root user mode to prevent container escape.
- Use Secret Management Services to Share Secrets Securely.
- Implement Read-Only File Systems and Volumes for Docker Containers.
- Perform Docker Image Vulnerability Scanning with tools like Clair.
- Employ DevSecOps Practices for Application Security.
- Thoroughly Test Code for Vulnerabilities before Production Use.
- Limit the size of Docker images.
- Choose between Trusting a Private or Public Image Registry.
- Avoid Storing Sensitive Data in Docker files or Images.
- Keep Databases for Long-Term Storage Separate.
- Conduct Open Source Vulnerability Checks using tools like Snyk (https://snyk.io/).

Testing Docker Containers
- Create a Docker container following this guide: https://docs.docker.com/compose/gettingstarted/
- Install the Snyk CLI from: https://docs.snyk.io/snyk-cli/install-the-snyk-cli
- Use the Snyk command `snyk container test <container_name>` to test your Docker image or Use the Snyk website for container security assessment.

Image Vulnerability Scanning
Utilize image vulnerability scanning tools such as:
- Clair - https://www.redhat.com/en/topics/containers/what-is-clair
- AWS Inspector - https://aws.amazon.com/inspector/
  
Secrets Management Tools
Consider using the following Secret Management Services:
- HashiCorp Vault - https://www.vaultproject.io/
- AWS Secrets Manager - https://aws.amazon.com/secrets-manager/

Running Containers in AWS
To address limitations with Docker and simplify deployment, consider using managed container services like:
- AWS Elastic Kubernetes Service (EKS)
- AWS App Runner
- AWS CoPilot
- AWS Elastic Container Service (ECS)
- AWS Fargate (Serverless Service)


# Setting up Notifications Page Backend and Frontend
Now that we have our Crudder app up and running with the default page setup, we will log in to Crudder with our email ID and check what are the scope of improvements.

First, go to frontend directory `gitpod /workspace/aws-bootcamp-cruddur-2023/frontend-react-js (main)` and do `npm i`
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/0b391359-a6de-40d0-880c-471f89040aa3)

Now right click on `docker-compose.yml` file in the VScode File section and compose up
Navigate to the URL in the "exposed ports" section for port 3000 (make sure both port 3000 and 4567 are unlocked)
Click on sign up, note this is a mocked-up website made by Andrew for testing purposes. Put all the details like your Email ID, Username, and Password

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/48d3bc9f-6d34-4689-ac20-d89fe3d18a4c)


On the Confirm your Email page, put your Email ID again, and the hardcoded password `1234`

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/19e9910c-1997-4068-9bc3-c87d177e309f)

Woohoo! Now I am logged in using my own username
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/4f051c32-2489-4de9-a4de-fd29e0e0b4a3)

Upon navigating through different pages, we understood that the "Notifications" page is yet to setup
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/1ad107d0-2245-46e7-ac95-8b1fc178e10d)

## Steps to setup the Notifications Page
### Backend:

1. Update the openapi file for Notifications
   ![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/012e1e9a-ec4f-4aac-88f5-8532b3d38176)

2. Add a new route to app.py file
```
@app.route("/api/activities/notifications", methods=['GET'])
def data_notifications():
  data = NotificationsActivities.run()
  return data, 200

```

3. Add new service in app.py file
   `from services.notifications_activities import *`
   
4. create a new file named notifications_activities.py and add below
```
   from datetime import datetime, timedelta, timezone
class NotificationsActivities:
  def run():
    now = datetime.now(timezone.utc).astimezone()
    results = [{
      'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
      'handle':  'Dhana',
      'message': 'Cats will rule over us one day! change my mind!',
      'created_at': (now - timedelta(days=2)).isoformat(),
      'expires_at': (now + timedelta(days=5)).isoformat(),
      'likes_count': 5,
      'replies_count': 1,
      'reposts_count': 0,
      'replies': [{
        'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
        'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Kitty',
        'message': 'Simba the cat cheetos-My lordship!!',
        'likes_count': 0,
        'replies_count': 0,
        'reposts_count': 0,
        'created_at': (now - timedelta(days=2)).isoformat()
      }],
    },
    ]
    return results

```

5. Save and commit everything and restart the application once. To test if the backend notifications page works as intended, go to https://4567-ddhaneumd-awsbootcampcr-0q5hn557mhe.ws-us104.gitpod.io and append `/api/activities/notifications` at the end of URL

   ![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/60d9b5b6-cbc2-456a-8112-5196d41203a2)

### Frontend

1. Under `/workspace/aws-bootcamp-cruddur-2023/frontend-react-js/src/App.js` add the following
   
   `import NotificationsFeedPage from './pages/NotificationsFeedPage';`
   
scroll down to the paths section and add

```
  {
    path: "/notifications",
    element: <NotificationsFeedPage />
  },
```

2. Under `/workspace/aws-bootcamp-cruddur-2023/frontend-react-js/src/pages` create the `NotificationsFeed.js` and `NotificationsFeed.css` pages.
   Add the following code to the `NotificationsFeed.js` page ( we are copying most content from the Home page as it is similar)

```
   import './NotificationsFeedPage.css';
import React from "react";

import DesktopNavigation  from '../components/DesktopNavigation';
import DesktopSidebar     from '../components/DesktopSidebar';
import ActivityFeed from '../components/ActivityFeed';
import ActivityForm from '../components/ActivityForm';
import ReplyForm from '../components/ReplyForm';

// [TODO] Authenication
import Cookies from 'js-cookie'

export default function NotificationsFeedPage() {
  const [activities, setActivities] = React.useState([]);
  const [popped, setPopped] = React.useState(false);
  const [poppedReply, setPoppedReply] = React.useState(false);
  const [replyActivity, setReplyActivity] = React.useState({});
  const [user, setUser] = React.useState(null);
  const dataFetchedRef = React.useRef(false);

  const loadData = async () => {
    try {
      const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications`
      const res = await fetch(backend_url, {
        method: "GET"
      });
      let resJson = await res.json();
      if (res.status === 200) {
        setActivities(resJson)
      } else {
        console.log(res)
      }
    } catch (err) {
      console.log(err);
    }
  };

  const checkAuth = async () => {
    console.log('checkAuth')
    // [TODO] Authenication
    if (Cookies.get('user.logged_in')) {
      setUser({
        display_name: Cookies.get('user.name'),
        handle: Cookies.get('user.username')
      })
    }
  };

  React.useEffect(()=>{
    //prevents double call
    if (dataFetchedRef.current) return;
    dataFetchedRef.current = true;

    loadData();
    checkAuth();
  }, [])

  return (
    <article>
      <DesktopNavigation user={user} active={'Notifications'} setPopped={setPopped} />
      <div className='content'>
        <ActivityForm  
          popped={popped}
          setPopped={setPopped} 
          setActivities={setActivities} 
        />
        <ReplyForm 
          activity={replyActivity} 
          popped={poppedReply} 
          setPopped={setPoppedReply} 
          setActivities={setActivities} 
          activities={activities} 
        />
        <ActivityFeed 
          title="Notifications" 
          setReplyActivity={setReplyActivity} 
          setPopped={setPoppedReply} 
          activities={activities} 
        />
      </div>
      <DesktopSidebar user={user} />
    </article>
  );
}

```

6. Now save all the pages, navigate to the frontend directory, and perform `npm i` Also from the main directory, perform `compose up` on docker compose file.

Finally, navigate to our website and see the notifications tab.

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/6985bdd7-5211-48b9-817d-178f970d3573)

Don't forget to check my cruds which show my love for cats xD!!
