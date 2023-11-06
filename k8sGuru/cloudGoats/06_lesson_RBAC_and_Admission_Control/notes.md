# RBAC and Admission Control


## Big Picture

![](../images/Screenshot%202023-11-06%20at%2018.55.16.png)

Kubernetes is API-centric. being API centric, means everything in the cluster revolves around the API server

normal interaction with the API server looks like this:

- You've got your client here making a request
- for the most part this client is gonna be kubectl,
or at least for users it is
- the client talks to the API server,
and it's HTTPS
- the certificates and stuff
are all done by the installer
- once all the TLS is established,
the authentication phase kicks in.
Now, sometimes we call this authN
- this phase is all about looking at the request
and saying, right, this request to do whatever,
maybe spin up a new deployment,
well, it's been requested by the user Nigel.
Well, how do we make sure it actually is Nigel?
- Well, after authentication, it's authorization,
and this is the RBAC bit, right?
That bit that says, "Okay, now that we know this.
"Is Nigel actually allowed to perform this action?"
Create the deployment or whatever, yeah?
- Next up is this thing called Admission control.
So, once the authN and the authZ stuff is done,
we can configure admission controllers
to modify and validate requests.


## Authentification (AuthN)

![](../images/Screenshot%202023-11-06%20at%2019.10.18.png)

Kubernetes doesn't manage user. users are manage externally


![](../images/Screenshot%202023-11-06%20at%2019.12.29.png)


![](../images/Screenshot%202023-11-06%20at%2019.15.55.png)

## Authorization (AuthZ)


![](../images/Screenshot%202023-11-06%20at%2019.17.59.png)
![](../images/Screenshot%202023-11-06%20at%2019.20.32.png)

![](../images/Screenshot%202023-11-06%20at%2019.22.17.png)
## Admission Control
Admission Control manage webhooks.

![](../images/Screenshot%202023-11-06%20at%2019.27.56.png)