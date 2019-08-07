# AWS-EC2-NGINX-OBS-Multi-Streaming with Stunnel for RTMPS
AWS-EC2/NGINX/OBS Multi Streaming for Free!

## 1. Initialization: EC2 Setup  
* Log in to AWS  
* Go to AWS Console  
* On Instances, Launch Instance  
* Choose (Free Tier) Ubuntu Server 16.04 LTS (HVM)  
* Choose Free Tier Instance Type  
* Skip to Step 6 - Configure Security Group  
* Add rules to anywhere
  - Add Rule SSH
  - Add Rule RDP
  - Add Rule HTTP  
  - Add Rule HTTPS
  - Add Rule Custom port 1935
* Review and Launch , Ignore warning, Launch  
* Choose a key pair or create a new one your choice  
* EC2 Setup - Done *  

## 2. AWS: Ubuntu Server Setup  
```
ssh to the server: example AWS EC2 Public DNS = ec2-12-123-123-123.us-west-1.compute.amazonaws.com
ssh -i "keypair.pem" ubuntu@<AWS EC2 Public DNS>
```

## 3. Update the server and install nginx  
```
sudo apt-get update
sudo apt-get install nginx
sudo apt install libnginx-mod-rtmp
```

## 4. allow UFW (firewall)
```
sudo ufw allow 'Nginx HTTP'
```

## 5. Start Nginx  
```
sudo systemctl stop nginx.service
sudo systemctl start nginx.service
sudo systemctl enable nginx.service
```

## 6. if you are successfull sample output below:  open browser and visit IPv4 Public IP in EC2
![alt text][logo]

[logo]: https://github.com/ohmcodes/AWS-EC2-setup-and-nginx-wordpress-postgresql-configurations/blob/master/default_apache.png?raw=true

## 7. Configure Nginx
```
sudo nano /etc/nginx/nginx.conf
```

Paste Code Below:  
```
rtmp {
	server {
		listen 1935;
		chunk_size 4096;

		application live {
			live on;
			record off;
      
      			#RTMP EXAMPLES
      			#Facebook
			push rtmp://rtmp-pc.facebook.com:80/rtmp/{stream_key};
      			#Twitch
      			push rtmp://live-sea.twitch.tv/app/{stream_key};
      			#Youtube
      			push rtmp://x.rtmp.youtube.com/live2/{stream_key};
      
		}
	}
}
```


## Ingest LINK for better stream

### Twitch
https://stream.twitch.tv/ingests/
### Facebook
https://www.facebook.com/live/ingests/



### Installing stunnel4 & Config
```
sudo apt install stunnel4

sudo nano /etc/stunnel/stunnel.conf

setuid = stunnel4
setgid = stunnel4
pid=/tmp/stunnel.pid
output = /var/log/stunnel4/stunnel.log
include = /etc/stunnel/conf.d

sudo nano /etc/default/stunnel4

ENABLE=1

sudo mkdir /etc/stunnel/conf.d/fb.conf

[fb-live]
client = yes
accept = 127.0.0.1:19350
connect = live-api-s.facebook.com:443
verifyChain = no

sudo systemctl restart stunnel4 && systemctl status stunnel4

push rtmp://127.0.0.1:19350/rtmp/<facebook-live-stream-key>;
```



