vagrant-haproxy
====================

* The three hosts are haproxy (54.164.36.35), web1 (54.174.90.158), and web2 (52.55.245.175)
* It sets up the following port forwards between your host's external interface and the internal VM's:

| Host port | Guest machine | Guest port | Notes
------------|---------------|------------|---
| 8080 | haproxy | 8080 | HAProxy Admin Interface
| 8081 | haproxy | 80 | Load Balanced Apache
* It installs Apache on the two web servers, and configures it with a index page that identifies which host you're viewing the page on.
* It installs HAProxy on the haproxy host, and drops a configuration file in place with the two webservers pre-configured.  It doesn't require HAProxy to be the default gateway because it NAT's the source IP as well as the destination IP.

# Prerequisites
1.  Install [Vagrant](http://www.vagrantup.com/downloads.html)
2.  Install [Virtualbox](https://www.virtualbox.org/wiki/Downloads)

# Getting started
1.  Open 3 terminal windows -- one for each host.  Change to the directory containing the Vagrantfile from step 3 above.
2.  In terminal #1, run ``` vagrant up haproxy && vagrant ssh haproxy ```
3.  In terminal #2, run ``` vagrant up web1 && vagrant ssh web1 ```
4.  In terminal #3, run ``` vagrant up web2 && vagrant ssh web2 ```
5.  Open up [http://localhost:8080/haproxy?stats](http://localhost:8080/haproxy?stats) in your host's browser.  This is the HAProxy admin interface.
6.  Open up [http://localhost:8081/](http://localhost:8081/) in your host's browser.  This is the load balanced interface to the two web servers.  **Note** this is port forwarded via your actual host, and will be accessible via your externally accessible IP address - you can access test the load balancer from another workstation if you wish.
7.  Open up [http://172.28.33.11/](http://172.28.33.11/) in a browser to see if web1's Apache is working.
8.  Open up [http://172.28.33.12/](http://172.28.33.12/) in a browser to see if web2's Apache is working.
5.  To see the Apache access logs on web1 and web2, run ``` sudo tail -f /var/log/apache2/access.log ```  If you'd like to filter out the "pings" from the load balancer, run ``` sudo tail -f /var/log/apache2/access.log | grep -v OPTIONS ```
6.  To stop Apache on one of the webservers to simulate an outage, run ``` sudo service apache2 stop ```  To start it again, run ``` sudo service apache2 start ```
7.  To make changes to haproxy, edit the config file with ``` sudo nano /etc/haproxy/haproxy.cfg ```  When you want to apply the changes, run ``` sudo service haproxy reload ```  If you break things and want to reset back, just run ``` sudo cp /etc/haproxy/haproxy.cfg.orig /etc/haproxy/haproxy.cfg && sudo service haproxy reload ```



