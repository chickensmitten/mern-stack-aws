# Deploy MERN Stack AWS

## AWS S3, for image upload details
- server/controllers/category.js
- client/pages/admin/category/create.js

## AWS SES, for sending email details
- server/helpers/email.js

## Setup server
- push code to github
- allow EC2 access to IAM user
- login to Ec2 ubuntu, click "Connect", copy and paste the ssh command `ssh -i "<something>.pem" @<AWS ID>.compute-1.amazonaws.com`
- setup EC2
```
// install npm
sudo apt update
sudo apt install nodejs npm
// install node
npm install node
```
- create ubuntu instance
- SSH into instance
- pull code on the server from github
- install dependencies and env variables
```
touch .env
vim .env //press i, then paste everything, then press esc, wq
vim next.config.js //press i, then paste everything, then press esc, wq
```
- start node application using pm2
```
npm i pm2 -g
pm2 start server.js
```
- configure nginx to make app (both backend/frontend) to run on port 80

## Setup client
- cd into it, then npm install, then npm run build
- `pm2 start npm -- start`

## Setup nginx
- `sudo apt install nginx`
- `nginx`
- `sudo apt nginx-full`
- cd into nginx `cd /etc/nginx`
- `sudo vim default`, then comment out the existing `location` block, then add
```
location / {
       proxy_pass http://127.0.0.1:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
   }
 
   location /api {
       proxy_pass http://127.0.0.1:8000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
   }
```
- then run `sudo service nginx restart`
- `sudo nginx -t` to test if all configuration is correct

## Making changes to code
- push code to github
- in EC2 ubuntu, pull with `sudo git pull`
- `npm run build` if client

## Setup domain name to app
- Use Amazon Route53
- Head to DNS Management
- Create hosted Zone
- Add domain name
- create record set by adding the amazon EC2 IP address.
- in the domain name service, add the .aws.dns values