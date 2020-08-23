# Guide to install Jitsi Video Conferencing System   
# into an LXD container

***Jitsi is a great Open Source Video Conferencing system you can self-host*** and works well on linux.

In this time of the COVID Pandemic use of Video Conferencing for work, family, friends has become a must.

Zoom is one example video conferencing system but is limited to a 45 min conference unless you are a paying customer.

In this Guide I am using Ubuntu 20.04, SNAP LXD and creating a 20.04 LXD container to create a self-hosted Jitsi server running in an LXD container.

Lastly, I have found that as of now users should ONLY use Chrome or Chromium for Jitsi to work.

The Jitsi/Firefox combo, even if only used by 1 conference attendee seems to create a problem the Jitsi developers are still working on.

Assumes you have LXD installed and a webcam & mic

### First...

If you already use LXD containers you know how easy LXD is to learn compared to Docker, Kubernetes.

However, thats a bit apples-oranges as LXD supports "system" containers. By System containers we mean a container runs a full Distro, each container has its own init, systemd etc. and can conceptually be thought of as just running a "server" except LXD like Docker containers share the Kernel of the Host machine.

LXD can create & run LXD containers of Debian, Fedora, CentOS, Alpine, Oracle Linux, OpenSuse, Ubuntu etc

I'm an Ubuntu user but I can run LXD containers of any of the above and do.

In LXD the "default" Host/Container interface is called "lxdbr0". lxdbr0 implements a dnsmasq/dhcp/NAT

to containers using it. Containers using lxdbr0 all get a private/non-internet non-Internet routable 10.x.x.x address.

However, LXD also supports different Host/Container interfaces such as MacVlan, "routed", "ipvlan" and generic "br0" type bridges

To install Jitsi in an LXD container, this guide is going to create a "routed" LXD for our jitsi container so the Jitsi Container 
will get an IP **on the same Network/Subnet** as your Host.

If you've not used/seen Jitsi before you should check out ***Jitsi's online demo system*** so you know what you will end up with: **https://meet.jit.si/**


## CIAB Guide for Self Hosting Jitsi Video Conferencing

### Prep Work:

If you've never looked at or edited an LXD Profile the first thing I'd do is create a copy of LXD's "default" profile.

You can call it whatever you want.

$ **lxc profile copy default default.copy**

Next you should set your default linux text editor in your Host to what you like to use. It defaults to VI but if you use "nano" for instance:

$ **export EDITOR=nano**

To make the change ti nano permanent, run the following once:

$ **echo 'export EDITOR=nano' >> ~/.profile**

$ **source ~/.profile**


### STEP 1

Create a new LXD profile to use with Jitsi by copying LXD's "default" profile.

I'll call my new profile "jitsiprf"

$ **lxc copy default jitsiprf**

### STEP 2

Edit the new *jitsiprf* profile.

$ **lxc profile edit jitsiprf**

Make it look like below...

https://github.com/bmullan/Guide-to-Install-Jitsi-in-LXD-container/blob/master/routed%20profile.png

Save your changed "**jitsiprf**" profile file.

### STEP3

Create a new LXD Container to run your Jitsi in but add your new "jitsiprf" profile to be included during its creation

$ **lxc launch ubuntu:20.04 jitsi --profile default --profile jitsiprf**

When its done you can check that your new "jitsi" container has the IP address you assigned in the "jitsiprf". For the

example above the jitsi container should show 192.168.1.200

$ **lxc list**

### STEP4

$ **lxc exec jitsi bash**

\# **apt update && apt upgrade**

Install Jitsi into the Container you created in STEP3 (I'm showing you Jitsi's like for Ubuntu/Debian) they have instructions for other distro's to.

**https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-quickstart**

**Note:**

I installed everything down to the section Labeled "**Access Control**".

The only other change I did was Jitsi's recommendation (which only needs to be done if you have specific problem they mention:

> Proxy-Server (nginx):  
>  
>  If the installation is behind a proxying nginx server, **delete/remove**
>  
>  /etc/nginx/modules-enabled/60-jitsi-meet.conf.
>  
>  Then go to /etc/nginx/sites-available/your-conf and change it to listen on 443 instead of 4444 and restart nginx.


### STEP5

***Restart your container.***

### TEST

From your Host start Chrome/Chromium up and point it to your Jitsi container and you should see Jitsi's web page.

Start a new conference.

Use the Jitsi provided link provided for your new conference and go to another machine, start Chrome/Chromium

and goto "*that Jitsi link* you got earlier"

If I haven't forgotten or mistyped anything you should be good to go.


### References:

A good reference to learn more about LXD's different Interface types and the "routed" interface in particular refer to:

**https://blog.simos.info/how-to-get-lxd-containers-get-ip-from-the-lan-with-routed-network/**
