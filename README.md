# LMDS Stack 

<b>Linux Media Delivery System Stack based on Docker for Raspberry Pi</b>
Originally @GreenFrogSB, then modified using the lessons learned while deploying it on the Raspberry Pi 4 8GB


## My own deploy strategy
- install git using a command:
<pre><code>sudo apt-get install git</code></pre>

- Clone the repository with:
<pre><code>git clone https://github.com/GreenFrogSB/LMDS.git ~/LMDS</code></pre>

<i>Do not change name of the folder on your local system it should stay as is for the script to work properly</i>

- Enter the directory and run:

<pre><code>cd ~/LMDS</code></pre>

<ul>
<li> Use the <code>./deploy.sh</code> script to install docker.</li>
<li> Use the <code>./deploy.sh</code> to create stack.
</li><li> Copy <code>docker-compose_paprikodlak.yml</code> to <code>docker-compose.yml</code>. Check <code>id pi</code> for uid a gid.
</li><li> Run <code>docker-compose up -d</code>, let it bring it up and then <code>docker-compose down</code>.
</li><li> Use <code>sudo chown -R pi:pi volumes</code> to fix permissions.
</li><li> Fix the problem with <code>dhcpcd</code> dying after boot by putting <code>denyinterfaces veth*</code> to he beginning of <code>/etc/dhcpcd.conf</code>, as stated [here](https://www.raspberrypi.org/forums/viewtopic.php?t=275497). 
</li><li>Put <code>jdownloader-block.txt</code> into the download folder, run it manually using <code>docker exec -it jdownloader sh</code>, run jdownloader using <code>java -jar JDownloader.jar -norestart</code> and set it up. Then delete the <code>.txt</code> file.
</li><li>Set up the PIA openVPN: to download the OpenVPN configuration files and certs is: https://(www.privateinternetaccess.com/openvpn/openvpn-nextgen.zip), once you have downloaded the zip (normally a zip as they contain multiple ovpn files) then extract it to /config/openvpn/ folder (if that folder doesn't exist then start and stop the docker container to force the creation of the folder). If there are multiple ovpn files then please delete the ones you don't want to use (normally filename follows location of the endpoint) leaving just a single ovpn file and the certificates referenced in the ovpn file (certificates will normally have a crt and/or pem extension).
</li><li> To fix the problem with Bazarr on RPi 4, run <code>wget http://ftp.us.debian.org/debian/pool/main/libs/libseccomp/libseccomp2_2.5.1-1_armhf.deb</code>, <code>sudo dpkg -i libseccomp2_2.5.1-1_armhf.deb</code>
</li><li> For the services interlinking, use the IPV4 IPAM Gateway IP of the bridge. In Heimdall, use external RPi IP adress or its assigned domain name.
</li></ul>

## Menu

### Install docker
<p>First "Install docker" this might take a while. Process will install latest Docker and Docker-compose for ARM. When installation is completed you will be prompted to reboot, please do so before continuing.<p>

### Build LMDS Stack
<p>Next "Build LMDS Stack", select docker containers that you would like to pull and deploy. You do not have to select them all, select only the one you will use. Selecting only the one you need will reduce RAM consumption on your Pi what might be a problem on RPi 3, not so much on RPi 4 I guess. I do not have RPi 4 so I was only able to test it on my old RPi 3B SBC</p>

<p>You might like to install Portainer among all the other containers - Portainer is a graphical interface that lets you manage Docker engine - very useful tool if you don’t want to use Docker CLI.</p>

### Docker commands

<p>This small section contains few useful commands in case your Portainer is not started and you would like to get something done without the GUI.</p>


### Miscellaneous commands.

<p>There are three scripts that can be used in case you would like to disable swapping to your SD card. You might want to do it in order to extend life of your SD card. SD cards were not designed for intensive IO tasks, therefore using them like normal HDDs is not ideal. Swap file is used to offload your RAM in case of OS need to dump it somewhere. Swap will be quite often modify, what might wear out your SD card in a long run. In other hand if your OS is swapping a lot of data it means his RAM size is generally to small - this might be a case on RPi3 where we have only 1GB of RAM.</p>        

### Update LMDS Stack

<p>Each time you run <code>./deploy.sh</code> script will check GitHub repository for any updates and download them if available. You can also manually check for update using this option without running <code>./deploy.sh</code> script. Updates will not modify your configuration or any private files except the ones that are part of the LMDS logic. Some new functions might be added or new containers etc.</p>
