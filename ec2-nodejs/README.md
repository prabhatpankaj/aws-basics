# Installing node and system dependencies
Once in an SSH session the first thing to do is get Node.js. NVM (Node Version Manager) is a pretty great way to install Node.js and allows you to easily switch versions if required.

To install NVM just run this command (same as in the NVM installation instructions).

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash

source ~/.bashrc
```
install node

```shell
nvm install 7
nvm install 8
nvm install 9
```

To check node is ready to go just echo the version.

```shell
node --version
```

You can list available versions using ls-remote:

```shell
nvm ls-remote
```

And then in any new shell just use the installed version:

```shell

nvm use node 8
```

Or you can just run it:

```shell
nvm run node --version

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
# Serving HTTP traffic on the standard port, 80

* install restart nginx and reload it 

```
sudo apt install nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
```

* update configuration file .

```
sudo rm -rf /etc/nginx/sites-available/default
sudo nano /etc/nginx/sites-available/default
```

* update default file

```shell

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
* restart nginx and reload it 

```
sudo apt-get update -y
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl status nginx
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
pm2 start server/index.js --name "nodejs"
```
┌────────┬────┬──────┬────────┬───┬─────┬───────────┐
│ Name   │ id │ mode │ status │ ↺ │ cpu │ memory    │
├────────┼────┼──────┼────────┼───┼─────┼───────────┤
│ nodejs │ 0  │ fork │ online │ 0 │ 0%  │ 22.4 MB   │
└────────┴────┴──────┴────────┴───┴─────┴───────────┘

Running PM2 as a service

After running the code above, it is recommended that you setup PM2 as a service so that it can start when the server starts.

```
env PATH=$PATH:/usr/local/bin pm2 startup -u ubuntu
```
To setup the Startup Script, copy/paste the following command:

```
sudo env PATH=$PATH:/home/ubuntu/.nvm/versions/node/v8.16.1/bin pm2 startup systemd -u ubuntu --hp /home/ubuntu
```
change the node version if otherthen v8.16.1

Finally, save the current running processes so they are run when PM2 restarts.

```shell
pm2 save
```
That’s it! You can log out/in to SSH, even restart your server and it will continue to run on port 80.

List running pm2
```
 pm2 ls
```
┌────────┬────┬──────┬────────┬───┬─────┬───────────┐
│ Name   │ id │ mode │ status │ ↺ │ cpu │ memory    │
├────────┼────┼──────┼────────┼───┼─────┼───────────┤
│ nodejs │ 0  │ fork │ online │ 0 │ 0%  │ 41.3 MB   │
└────────┴────┴──────┴────────┴───┴─────┴───────────┘

restart running pm2 by name

```
pm2 restart nodejs
```
┌────────┬────┬──────┬────────┬───┬─────┬───────────┐
│ Name   │ id │ mode │ status │ ↺ │ cpu │ memory    │
├────────┼────┼──────┼────────┼───┼─────┼───────────┤
│ nodejs │ 0  │ fork │ online │ 1 │ 0%  │ 15.2 MB   │
└────────┴────┴──────┴────────┴───┴─────┴───────────┘
