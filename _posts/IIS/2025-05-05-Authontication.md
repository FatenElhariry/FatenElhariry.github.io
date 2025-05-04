---
layout: post
title: IIS
author: Faten Elhariry
permalink: /posts/IIS/Authontication
---
- **Anonymous**
each application pool is attached to default user with the same name as the application pool, if the impersonation is disabled

> if impersonation is enabled the application will be **classic** as the pipline mode for the request will be different
- **Basic**, **digest**
both use windows authontication  
> digest has to use domain because it's related to AD 
![difference between basic and digest](/images/IIS/image%20copy%202.png)
- add the authontication feature to the iis 
![install iis feature](/images/IIS/image%20copy%203.png)
- add user 
![add user](/images/IIS/image%20copy%204.png)

*digest*
1. add domain server - AD
2. add user to AD users 
- **Windows**
the same as before except for loopback check, where the user will redirect to the login towice, encrypted 
- **Forms**