# CanDIG Technical Onboarding
## CanDIG Project Overview
The CanDIG project has a distributed architecture, meaning each site is meant to run their own _____(What do you call everything represented in the diagram together) and there is no centralized CanDIG server. Users can use their local CanDIG _____(^) to communicate with other sites. Every site is responsible for handling the authentication of it's users and the authorization of incoming requests from other sites. CanDIG uses an API driven approach, where users send requests to their CanDIG which communicate with other sites through ...?
## Component Overview:
### Tyk [Gateway, Pump]
Tyk Gateway is an API managment platform that CanDIG uses to map API endpoints to endpoints in CanDIG server. When the user makes a request, it is made to Tyk, which forwards the request to the mapped endpoint in CanDIG server.  
Tyk Pump collects metrics on the use of Gateway and has many methods for transmiting these metrics to various databases.
### Keycloak
Authentication. That's it.?
### CanDIG server
Fasciliates the communication between different sites running CanDIG. What else?
### mongodb, redis
Databases. What do they store? Absolutely no idea. Maybe this is what pump goes to?

## How they interact:
![Architecture Diagram](https://github.com/CanDIG/PublicDocuments/tree/master/Onboarding/Figures/ArchitectureDiagram.png)

Tyk Gateway maps endpoints from tyk to CanDIG server so that the changing of endpoints is easy and requires no work on the servers side.  
All authentication is done through Keycloak for SSO and security. If authentication is required, gateway redirects to keycloak via a virtual endpoint to a JS script.  
Where do mongo and redis come in?


Main Questions: 
1) Anything that has a '?' above
2) How's the diagram looking?
3) Level of Detail
4) How do individual sites communicate, is it as simple as an api call to the other server?
5) More diagrams?
6) Should I be playing with Aman's branch now instead due to the dashboard removal
7) Coming soon to a PR near you.