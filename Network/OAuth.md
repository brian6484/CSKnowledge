# OAuth (Open Authorisation)

## Diff between authentication and authorisation
Authentication - identifying who you are (like via login or making account)
Authorisation - whether you have the authority to do this particular action

## Terminology of OAuth2
Resource server - server that hosts and manages the protected resources that client wants to access. It is 
responsible for handling requests to these resources, validating tokens, etc

Authorisation server - authenticates resource owner, obtains their consent and issuing access tokens to client. It just
authorises client to access requested resources *on behalf of resource owner*

Resource owner - you

Client - our service that we are making that requests to access the resource owner's resources. It initates the OAUTH2 
flow and obtains access tokens to access the protected resources

Access token - credential which represents the authorisation for client to access protected resources of resource
owner. Client can pass this time-limited access token to resource server to request access for the resources

Refresh token - optional credential where client can get access token without needing to do this whole oauth2 flow since
access token is time-limited

## Flow 
![166139288-61b86220-5cd8-4ee3-b577-4d437abdfa5c](https://github.com/brian6484/CSKnowledge/assets/56388433/14002b65-d041-4a34-8886-542a8e3df403)


