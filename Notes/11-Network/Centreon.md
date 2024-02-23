### Network Configuration

#### 1. OPNsense + VM Installation

- Install OPNsense
- Configure network interfaces:
    - 1 NAT
    - 1 Zone 1
    - 1 Zone 2
- Assign interfaces (em0, em1, and em2)
- Set interfaces (em1 → Zone 1, em2 → Zone 2)

#### 2. Machines Network Configuration

- Configure machines' network cards according to the network zone.

#### 3. Machines and Network Configuration

- Install Active Directory and Windows DNS server
- Configure static IP addresses for machines
- Add a firewall rule on OPNsense to allow all entries on (OPT1 Zone2)
- Change the Debian client's IP address to static
- Configure IP addresses for collectors and the central server

#### 4. Connectivity Testing

- Test pings between machines
- Verify connectivity to google.fr

### Centreon Installation

#### 5. Centreon/Central Installation

- Follow the Centreon documentation for central installation
`https://download.centreon.com/`
- Execute the command found in the documentation
- Connect to the Centreon web interface
- Verify "central running"
- Install Linux SNMP and Windows SNMP via the Monitoring Connector Manager

#### 6. Centreon/Collector Installation

- Follow the Centreon documentation for collector installation
`https://docs.centreon.com/en/docs/installation/installation-of-a-poller/using-packages/`
---
`https://docs.centreon.com/en/docs/monitoring/monitoring-servers/add-a-poller-to-configuration/`
- Add a poller in Configuration → Pollers from the central or collector
- Follow the documentation to attach a collector to a central server
- Export the configuration of the collector and central

#### 7. Host Configuration

- Add hosts with CLAPI on the central
```ruby
centreon -u admin -p 'centreon' -o HOST -a ADD -v "test;Test host;127.0.0.1;generic-host;central;Linux"
```
- Install NRPE on the collector following the Linux NRPE3 documentation:
`sudo dnf update`

Find the allowed_hosts configuration directive and add the IP address of your Nagios or Centreon server:
`allowed_hosts=127.0.0.1,<Nagios_or_Centreon_Server_IP>`
    
- **Install NRPE Client:** Install the NRPE client package.
`sudo dnf install nrpe`
    
- **Enable and Start NRPE Service:** Enable the NRPE service to start on boot and start the service.
`sudo systemctl enable nrpe sudo systemctl start nrpe`
    
- **Configuration:** The NRPE client configuration file is usually located at `/etc/nrpe.cfg`. Edit this file to allow communication from your Nagios or Centreon server.
`sudo nano /etc/nrpe.cfg`

- Install NRPE server on the Debian client via command line
```ruby
sudo apt install nagios-nrpe-server nagios-plugins
```
- Configure nrpe.cfg files for collector and Debian machines
- Verify NRPE functionality with the documentation command
```ruby
/usr/lib/nagios/plugins/check_nrpe -V
```
- Create a probe on Debian and place it in a folder with low privileges
- Modify the NRPE configuration file on Debian, then restart everything
- Add the command and create a service with it

#### 8. Finalization

- Remember to export configurations when making changes to pollers
- You should be able to monitor OPNsense, AD, the client with your probe, and Centreon plugins