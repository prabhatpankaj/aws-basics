# Installing node and system dependencies
Once in an SSH session the first thing to do is get Node.js. NVM (Node Version Manager) is a pretty great way to install Node.js and allows you to easily switch versions if required.

To install NVM just run this command (same as in the NVM installation instructions).

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash

source ~/.bashrc
```
install node

```shell
nvm install 7
```
To check node is ready to go just echo the version.

```shell
node --version
```
# Creating a public HTTP endpoint
we are going to make a public URL anyone can request from the browser to get a response from the server.

Make a directory for the server and cd into it.

```shell
mkdir server
cd server
```
Now you are in your server directory, you need to npm init

```shell
npm init
```
This will create a package.json file which will be used to track any dependencies we use. npm init will ask for a load of info, I tend to just press enter to use all the defaults.

All we will need to run our server is the express package. To install express and add it to package.json.

```shell
npm install express --save-dev
```
Now we just need to add some code to run the server. We will use nano to write the server in an index.js file.

```shell
cat > index.js <<EOF
const express = require('express')
const app = express()
app.get('/', (req, res) => {
  res.send('HEY!')
})
app.listen(3000, () => console.log('Server running on port 3000'))
EOF
```
The server I used just responds with “HEY!” when you do a request to /. I listen to requests on port 3000.

go to the Security Groups tab in the EC2 console. Right click the security group you setup and click edit inbound rules. Click Add Rule. This time we are going to use a custom TCP rule on port 3000, open to anywhere.

Now we can use node to start the server!

Using a browser, visit your public DNS URL with port 3000 and you should see the HEY! response.


```shell
node index.js
```


```
sudo rm -rf /etc/nginx/sites-available/default
sudo nano /etc/nginx/sites-available/default

* update default file
```
server {
       listen 80;
       listen [::]:80;

       server_name 54.157.197.164;

       location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
* install restart nginx and reload it 

```
sudo apt install nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
```
Using a browser, visit your public DNS URL and you should see the HEY! response.

To leave the server running when we log out, we need to press ctrl+z to pause the process (this only works when your server is running, node index.js). When you press ctrl+z you will be presented with all jobs, in this case the only one there will be the Node.js job that was paused.

You can see that the job number for node index.js is 1 (as noted by [1]+). To run that in the background, use the bg command.
```shell
bg %1
```
Then logout
```shell
exit
```
You should still be able to access your URL and see the “HEY!” response.

# Serving HTTP traffic on the standard port, 80

As of now app is running on port 3000 . we need to redirect traffic between 3000 and 80 using router .

There are few great choices for a router, but I find that nginx is generally the best tool for most things. It’s great for building your first ever app, or when you need to scale up to millions of visitors.

Using apt-get, we can install nginx in one command.
```shell 
sudo apt-get install nginx
```
apt-get runs nginx automatically after install so you should now have it running on port 80, check by entering your public DNS URL into a browser.

you should get "WELCOME TO NGINX!" page.

If this doesn’t work, you might need to start it manually.

```shell
sudo /etc/init.d/nginx start
```
We need to configure nginx to route port 80 traffic to port 3000. nginx has config placed in the /etc/nginx/sites-available folder where there is already a default config which serves the nginx welcome page we saw earlier.

How are nginx configs set up? Configs are stored in plain text files in sites-available with any name. Linking them into the sites-enabled folder will cause them to be read and used when nginx starts. All of the configs are combined together by nginx.

Let’s first remove the default config from sites-enabled, we will leave it in sites-available for reference.

```shell
sudo rm /etc/nginx/sites-enabled/default
```
Create a config file in sites-available and name it whatever you like.

```shell
sudo nano /etc/nginx/sites-available/nodejsapp
```
The following is the config we are going to use.

```shell
server {
  listen 80;
  server_name nodejsapp;
  location / {
    proxy_set_header  X-Real-IP  $remote_addr;
    proxy_set_header  Host       $http_host;
    proxy_pass        http://127.0.0.1:3000;
  }
}
```
This will forward all HTTP traffic from port 80 to port 3000.

Link the config file in sites enabled (this will make it seem like the file is actually copied insites-enabled).

```shell
sudo ln -s /etc/nginx/sites-available/nodejsapp /etc/nginx/sites-enabled/nodejsapp
```
Restart nginx for the new config to take effect.

```shell
sudo service nginx restart
```

Now visit your server’s public DNS URL, using port 80 and you should see the HEY! response.

# Keeping the Node.js process running

It’s quite tedious using ctrl+z to pause a process, and then running it in the background. Also, doing it this way will not allow the Node.js process to restart when you restart your server after an update or crash.

Before moving forward, stop your running node process

```shell
killall -9 node
```
To keep these processes running we are going to use a great NPM package called PM2. While in an SSH session, install PM2 globally.

```shell
npm i -g pm2
```
To start your server, simply use pm2 to execute index.js.

```shell
pm2 start server/index.js
```
To make sure that your PM2 restarts when your server restarts

```shell
pm2 startup
```
This will print out a line of code you need to run depending on the server you are using. Run the code it outputs.

Finally, save the current running processes so they are run when PM2 restarts.

```shell
pm2 save
```
That’s it! You can log out/in to SSH, even restart your server and it will continue to run on port 80.


