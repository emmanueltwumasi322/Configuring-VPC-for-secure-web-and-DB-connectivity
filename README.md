<h1>Configuring Amazon VPC Networking for Secure Web and Database Connectivity</h1>

<h2>Description</h2>

This project demonstrates how I configured networking components within an Amazon Virtual Private Cloud (VPC) to restore secure connectivity between a public web server and a private database server.

The environment initially experienced two networking issues:
<ul>
  <li>The Amazon EC2 web server could not be reached from the internet.</li>
  <li>The private database server could not securely communicate with the web server.</li>
</ul>

To resolve these issues, I updated the VPC route tables, configured an Internet Gateway, and modified Security Group rules. The final architecture follows AWS networking best practices by allowing internet access only to the public web server while keeping the database isolated inside a private subnet.

<br />

<h2>Architecture Overview</h2>

<ul>
<li><b>Amazon VPC</b> providing network isolation</li>
<li><b>Public Subnet</b> hosting the web server with a public IPv4 address</li>
<li><b>Private Subnet</b> hosting the database server</li>
<li><b>Internet Gateway (IGW)</b> providing internet connectivity to the public subnet</li>
<li><b>Route Tables</b> directing internet-bound traffic</li>
<li><b>Security Groups</b> controlling inbound and outbound traffic</li>
</ul>

<h2>Technologies Used</h2>

- <b>Amazon VPC</b>
- <b>Amazon EC2</b>
- <b>Security Groups</b>
- <b>Route Tables</b>
- <b>Internet Gateway</b>
- <b>AWS Management Console</b>

<h2>Environments Used</h2>

- <b>AWS Cloud</b>
- <b>Amazon EC2 Console</b>

<h2>Project Objectives</h2>

<ul>
<li>Explore the components that comprise an Amazon VPC.</li>
<li>Configure Route Tables attached to VPC subnets.</li>
<li>Configure an Internet Gateway for internet access.</li>
<li>Configure Security Group inbound and outbound rules.</li>
<li>Restore secure connectivity between web and database servers.</li>
</ul>

<h2>Network Design</h2>

<ul>
<li>Web Server deployed inside a <b>Public Subnet</b>.</li>
<li>Database Server deployed inside a <b>Private Subnet</b>.</li>
<li>Only the Public Subnet routes traffic through the Internet Gateway.</li>
<li>The database remains inaccessible directly from the internet.</li>
<li>The Web Server communicates with the Database over MySQL (Port 3306).</li>
</ul>

<h2>Walk-through</h2>

<p align="center">

<b>Step 1: Open AWS Console.</b><br/>
Verified the AWS Region was set to <b>US East (N. Virginia)</b> and opened the EC2 service from the AWS Console.
<br/><br/>



<br/><br/>

<b>Step 2: Test the Web Server.</b><br/>
Copied the Web Server's public IPv4 address and opened it in a browser. The webpage timed out, confirming the server could not be reached from the internet.
<br/><br/>

<img src="INSERT_SCREENSHOT_HERE" height="80%" width="80%" alt="Website Timeout"/>

<br/><br/>

<b>Step 3: Review the Web Server Networking Configuration.</b><br/>
Verified the instance networking information including its Public IPv4 Address, Private IPv4 Address, and associated subnet.
<br/><br/>

<img width="658" height="347" alt="Screenshot 2026-08-04 at 9 27 57 PM" src="https://github.com/user-attachments/assets/e1b4fc28-5f71-4430-a499-a0ecda18492b" />
 

<br/><br/>

<b>Step 4: Inspect the Route Table.</b><br/>
Opened the Route Table associated with the Web Server subnet and reviewed the existing routes.
The subnet was configured to send internet traffic through a NAT Gateway instead of an Internet Gateway.
<br/><br/>

<img width="944" height="647" alt="Screenshot 2026-08-04 at 9 48 53 PM" src="https://github.com/user-attachments/assets/6132787a-16e6-4780-b65a-897714e435b6" />

<br/><br/>

<b>Step 5: Replace the NAT Gateway Route.</b><br/>
Removed the NAT Gateway route and created a new default route:
<ul>
<li>Destination: <b>0.0.0.0/0</b></li>
<li>Target: <b>Internet Gateway (IGW)</b></li>
</ul>

This enabled direct internet connectivity for resources inside the public subnet.
<br/><br/>

<img width="763" height="505" alt="Screenshot 2026-08-04 at 9 55 43 PM" src="https://github.com/user-attachments/assets/631df0c2-2e19-4f2e-8d1f-f92505674e3d" />

<br/><br/>

<b>Step 6: Verify Internet Gateway Association.</b><br/>
Confirmed the Route Table now forwards internet traffic through the Internet Gateway.
<br/><br/>

<img src="INSERT_SCREENSHOT_HERE" height="80%" width="80%" alt="Updated Route Table"/>

<br/><br/>

<b>Step 7: Configure Web Server Security Group.</b><br/>
Opened the Web Server Security Group and modified the inbound rules.
Added an HTTP rule:
<ul>
<li>Protocol: HTTP</li>
<li>Port: 80</li>
<li>Source: Anywhere (IPv4)</li>
</ul>

This allows users to access the web server from the internet.
<br/><br/>

<img width="945" height="473" alt="Screenshot 2026-08-04 at 10 06 06 PM" src="https://github.com/user-attachments/assets/ac7cfba6-0600-4709-9d73-4247d560d39a" />

<br/><br/>

<b>Step 8: Configure Outbound Rules.</b><br/>
Reviewed the outbound rules and added an additional rule allowing:
<ul>
<li>All Traffic</li>
<li>Destination: 0.0.0.0/0</li>
</ul>

This allows the web server to communicate with external services when required.
<br/><br/>

<img src="INSERT_SCREENSHOT_HERE" height="80%" width="80%" alt="Outbound Rules"/>

<br/><br/>

<b>Step 9: Test Internet Connectivity.</b><br/>
Copied the Web Server's public IP address and opened it using HTTP.

The webpage loaded successfully, confirming that internet access had been restored through the Internet Gateway.
<br/><br/>

<img width="1220" height="660" alt="Screenshot 2026-08-04 at 8 45 31 PM" src="https://github.com/user-attachments/assets/63647943-9d60-4823-9e55-1b91082b20e3" />

<br/><br/>

<b>Step 10: Verify Database Connectivity.</b><br/>
The application diagram showed:
<ul>
<li>Internet → Web Server: <b>Successful</b></li>
<li>Web Server → Database Server: <b>Failed</b></li>
</ul>

This is expected because the Database Security Group still needs an inbound MySQL (Port 3306) rule allowing traffic only from the Web Server Security Group.
<br/><br/>

<img src="INSERT_SCREENSHOT_HERE" height="80%" width="80%" alt="Database Connectivity"/>

<br/><br/>

<h2>Security Improvements</h2>

<ul>
<li>Configured an Internet Gateway for the public subnet.</li>
<li>Updated Route Tables to direct internet traffic appropriately.</li>
<li>Allowed inbound HTTP traffic only on Port 80.</li>
<li>Restricted database placement to a private subnet.</li>
<li>Prepared the environment for secure MySQL communication over Port 3306.</li>
</ul>

<h2>Lessons Learned</h2>

During this lab, I gained hands-on experience configuring core AWS networking components within an Amazon VPC. I learned how Route Tables determine network traffic flow, how Internet Gateways provide internet access to public resources, and how Security Groups act as virtual firewalls controlling inbound and outbound traffic. I also reinforced the AWS best practice of isolating databases inside private subnets while exposing only necessary public-facing resources such as web servers.

<h2>Skills Demonstrated</h2>

<ul>
<li>Amazon VPC Configuration</li>
<li>Subnet Design</li>
<li>Route Table Management</li>
<li>Internet Gateway Configuration</li>
<li>Security Group Administration</li>
<li>AWS Networking</li>
<li>Cloud Infrastructure Troubleshooting</li>
<li>Public and Private Subnet Architecture</li>
</ul>
