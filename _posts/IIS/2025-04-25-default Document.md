---
layout: post
title: IIS
author: Faten Elhariry
permalink: /posts/IIS/defaultDocument
---

- [Default Document](#default-document)
- [FTP Server](#ftp-server)
- [Working with power shell](#power-shell)


### <a name="default-document">default document</a>
#### where can you find and what is the needs for it?
- it exist in the application features list 
- used to define the startup page for the application, otherwise application will return forbidden 


> **what is the entry type (inherited and local)?**  
>inherited means used from the server defination, local means define in the application itself 

### <a name="ftp-server">FTP Server</a>

##### installation 
- server features 
- add service or role 
- (iis feature), check **FTP server, FTP extensibility**
- next to findalize the installation
- add new application on the iis with ftp type and define the binding port and configuration 

### <a name="pwer-shell">Power Shell</a>
- `get-website`
  will list all the defined sites and the binding information(ports)
- `get-website -Name "slow.com" | stop-website`
- `get-website -Name "slow.com" | start-website`
- `New-website -Name "website1" -PhysicalPath c:\inetpub\website1`
  to create new website by the power shell
- 