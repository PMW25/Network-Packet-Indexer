# Network-Packet-Indexer
Project capturing Pcaps using the tool Arkime and implementing Elastic Search.


Installing & Configuring Arkime (Moloch) for PCAP Analysis

1. Purpose & Overview

Arkime is a scalable PCAP capture and analysis platform.

Uses Elasticsearch as its search database.

Allows indexing and searching across multiple PCAPs.

2. Server Setup

Ubuntu 22.04 server with 8GB RAM & 50GB disk.

Update and upgrade system:


	sudo apt-get update && sudo apt-get upgrade


3. Install Arkime

Download Arkime package for Ubuntu (matching CPU architecture) via wget.

Install:

	sudo dpkg -i <arkime_package>

Fix missing dependencies:

	sudo apt-get -f install

4. Configure Arkime:

Run:

	sudo /opt/arkime/bin/configure


Choose network interface (e.g., ens33).

For demo, install local Elasticsearch.

Set admin password and enable MaxMind geoip download (requires account).

Save setup instructions from Step 4 in Notepad.

5. Start Services

		sudo systemctl start elasticsearch.service
		sudo /opt/arkime/db/db.pl http://localhost:9200 init
		sudo /opt/arkime/bin/arkime_add_user <username> "<description>" <password> --admin
		sudo systemctl start arkimecapture.service
		sudo systemctl start arkimeviewer.service

6. Access Web Interface

URL: http://<arkime_server_ip>:8005

Login with created admin account.

Explore:

Sessions → View indexed traffic.

SPI View → Protocol breakdown.

Connections → Graph view.

Hunt → Create scheduled searches.

Integrating MaxMind GeoIP:

Create MaxMind account → Generate license key.

Install geoip update tool:


	sudo apt install geoipupdate


	Edit /etc/GeoIP.conf with account ID, license key, and edition IDs (GeoLite2-ASN, GeoLite2-Country, GeoLite2-City).

Run:


	sudo geoipupdate
	
 	sudo systemctl restart arkimecapture.service

Ingesting PCAPs


Download malicious PCAP from malware-traffic-analysis.net.

Unzip with password infected_2024 (example).

Create pcaps directory, move file there.

Ingest:



	sudo /opt/arkime/bin/capture --copy -MR .

Adjust time filter in Arkime UI to match PCAP capture date.

Analyzing Traffic
Sort by Bytes to find largest sessions.

Expand sessions to view HTTP, DNS, or raw packet data.

Filter by IP, domain, or protocol.

Export PCAPs for deeper analysis in Wireshark.

Adding Context OSINT Module


Configure Context:


	sudo /opt/arkime/bin/configure

Select Context.

Edit /opt/arkime/etc/context.ini:

Set http://localhost:9200 as Elasticsearch URL.

Add random passwordSecret.

Ensure port 3218 is uncommented.

Restart:
	
 	sudo systemctl restart arkimecontext.service

