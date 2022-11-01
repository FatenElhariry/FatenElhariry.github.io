---
layout: post
title: Identity Server  
author: Faten Elhariry
categories: development
tags: development
---

#### identity server

  is used yo implement the open id connection over the Oauth 2 protocal to authonticate and authorize the user using 
  scope, identity resources, resources and client.

  - we use middleware to enable the pipelines to define the endpoints 
  - allow interoperability 
  - it is important to use the identity server middleware before `useMVC` due to the identity server redirect the communication to the mvc endpoints 
  - **Scopes**
    are labels for the resources that identity server protected like the email of the user  
  - **identity resources**
    is what the identity resources told the other about me 

##### What is the differnece between openId vs OAuth2?
  - Oauth02
    is a open protocol that allow secure authorization in a simple and standarad message from web, mobile and desktop &rarr; it deals with authorization with access token and client application use it over web, mobile and desktop application.to access Api or endpoint it can't be used to sign-in to client application because it is **authorization tool**

    - OpenId Connect 
      is a simple identity layer on top of the Oauth2, it extend the Oauth2 with identity token + access token 
      - where the identity token can be used to sign-in to the application 

- **Terminology**
  <img src="../../../images/development/IDS.png" />
##### TST 
   