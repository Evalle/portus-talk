# Steps for the demo

Open http://172.17.0.1:3000 with your browser and perform the following steps:

 1. Create an admin account
 2. You will be redirected to a page where you have to register the registry. In this form:
   - Choose a custom name for the registry.
   - Enter 172.17.0.1:5000 as the hostname.
   - Do *not* check the "Use SSL" checkbox, since this setup is not using SSL.


Perform the following actions on the docker hosts that need to interact with your registry:


 - Ensure the docker daemon is started with the '--insecure-registry 172.17.0.1:5000'
 - Perform the docker login.


To authenticate against your registry using the docker cli do:

```
 $ docker login 172.17.0.1:5000
```

To push an image to the private registry:

```
 $ docker pull busybox
 $ docker tag busybox 172.17.0.1:5000/<username>/busybox
 $ docker push 172.17.0.1:5000/<username>/busybox
```
