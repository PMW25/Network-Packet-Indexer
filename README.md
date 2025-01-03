# Network-Packet-Indexer
Project capturing Pcaps using the tool Arkime and implementing Elastic Search.

What is Arkime?

Arkime is an open-source, large-scale, full-packet capture and indexing system that enables users to capture and store PCAP data efficiently. It works with Elasticsearch for storing and searching metadata.

Prerequisites
1.	Hardware Requirements:
	
  	•	CPU: At least 4 cores.

 	•	RAM: 16 GB or more.

 	•	Storage: Minimum of 500 GB (depending on expected PCAP storage needs).

 
 2.	Software Requirements:

 	•	Supported OS: Ubuntu (20.04 or higher) or CentOS (7/8).

 	•	Elasticsearch: Version 7.x (compatible with Arkime).

 	•	Arkime: Latest version from the official repository.

	•	Packet capture tool: tcpdump or Arkime’s built-in capture engine.

Step 1: Install Elasticsearch

1.1 Download and Install Elasticsearch
	
1.	Add the Elasticsearch GPG key:
	
		wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -


2.	Install the apt-transport-https package:

		sudo apt-get install apt-transport-https


3.	Add the Elasticsearch repository:

		echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list


4.	Update the package index and install Elasticsearch:

		sudo apt update
		sudo apt install elasticsearch


1.2 Configure Elasticsearch
	
1.	Open the Elasticsearch configuration file:

		sudo nano /etc/elasticsearch/elasticsearch.yml


2.	Configure the following:
	
 •	Set the cluster name:

cluster.name: arkime-cluster


•	Set the network host:

network.host: 0.0.0.0


•	Ensure Elasticsearch is accessible only from the Arkime host or secure it with a firewall.

3.	Save and close the file.
	
4.	Start and enable Elasticsearch:

		sudo systemctl start elasticsearch

		sudo systemctl enable elasticsearch


5.	Test the installation:

		curl -X GET "http://localhost:9200"

Step 2: Install Arkime

2.1 Download and Install Arkime

1.	Download the latest version of Arkime:

		wget https://s3.amazonaws.com/files.molo.ch/builds/arkime_3.4.0-1_amd64.deb


2.	Install the downloaded package:

		sudo dpkg -i arkime_3.4.0-1_amd64.deb



2.2 Configure Arkime
	
 1.	Run the setup script:

		sudo /opt/arkime/bin/Configure

•	Follow the prompts to configure:
•	Elasticsearch URL: http://localhost:9200
•	Admin user credentials (for Arkime web interface).
•	Network interfaces for packet capture (e.g., eth0).

2.	Verify the configuration file:

		sudo nano /opt/arkime/etc/config.ini

Key configurations:
	
•	Elasticsearch URL:

elasticsearch: http://localhost:9200


•	Capture directory for PCAPs:

		pcapDir=/data/pcap


•	Interface to capture packets:

		interface=eth0


3.	Create the PCAP storage directory:

		sudo mkdir -p /data/pcap

		sudo chown -R $USER:$USER /data/pcap

Step 3: Start Services
	
 1.	Start the Arkime capture service:

		sudo systemctl start arkimecapture


2.	Start the Arkime viewer service (web interface):

		sudo systemctl start arkimeviewer


3.	Enable both services to start on boot:

		sudo systemctl enable arkimecapture

		sudo systemctl enable arkimeviewer

Step 4: Access Arkime Web Interface
	
 1.	Open a browser and navigate to:

		http://<your-server-ip>:8005


2.	Log in with the admin credentials you set during configuration.
	
3.	Verify the PCAP data is being indexed and displayed in the Arkime interface.

Step 5: Capture and Store PCAPs
	
 1.	Start Capturing:
	
 •	Arkime automatically captures packets on the specified interface. To manually test:

		sudo /opt/arkime/bin/capture -r /path/to/pcapfile.pcap


•	Or use tcpdump to generate PCAPs:

		sudo tcpdump -i eth0 -w /data/pcap/test.pcap


2.	Search Captured Data:
	
 •	Use the Arkime web interface to search and analyze the captured packets.

 •	Filters include source/destination IP, protocol, or payload content.

Step 6: Verify Elasticsearch Integration
	
 1.	In the Elasticsearch database, confirm Arkime metadata is indexed:

		curl -X GET "http://localhost:9200/_cat/indices?v"

You should see indices like sessions2-*.

	
 2.	Use Arkime’s advanced search features to query indexed PCAP data.

Step 7: Customize and Optimize
	
 1.	Retention Policy:
	
 •	Set up a retention policy to delete old data:

		rotateIndex=7

(Deletes indices older than 7 days.)

	
 2.	Dashboards:

 •	Use Arkime’s built-in dashboards for visualizing network data or export metadata to external tools for further analysis.

 3.	Secure the Deployment:

 •	Use HTTPS for Arkime’s web interface and Elasticsearch.
	•	Configure authentication and limit access to trusted IPs.
