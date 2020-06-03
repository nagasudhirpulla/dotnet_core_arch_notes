### Why IdentityServer
* IdentityServer is a **Secure Token Service (STS)** that issues tokens for authenticating users or apps across applications
* This way the security concerns of api interactions and user interactions with apps are addressed by a single identity server
* This is just like "sign once with google to access all apps like gmail, drive, youtube"
* This is very useful for securing apps or users in a group of microservices working together
* IdentityServer issues security tokens based on **OAuth 2.0 and OpenID Connect** standards

### How it works
As shown below, IdentityServer issues tokens for authenticating users or apps across applications

![identityServerIllustration](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/identityServerIllustration.png)

### Using IdentityServer in our ASP.NET Core projects
While implementing our Identity Service, we use IdentityServer as a middleware in our services pipeline. Most the specs stuff (like api endpoints, workflow etc) is handled by the IdentityServer middleware and we need to implement some endpoints like user interface etc as shown below

![identityServerMiddlewareIllustration](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/identityServerMiddlewareIllustration.png)

### Terminology
![identityServerTerminology](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/identityServerTerminology.png)

As shown below the following is the terminology in the context of IdentityServer

#### Resource
It is something that is protected by the IdentityServer either APIs or users identity data
#### Client 
* Client is a software (like mobile app, web app, native app etc) that requests tokens from IdentityServer either for authenticating a user or for accessing a resource. 
* Client needs to be registered first before accessing the tokens. 
* Identity token is requested for user authentication and access token is requested for accessing a resource.

#### User
User is a human using a registered client to access resources

### IdentityServer
It is the token issuing security service that 
* authorizes APIs or users
* protects resources
* takes care of session management and single sign on
* manages and authenticate clients
* issue identity and access tokens to clients
* validate tokens

### Grant Types
https://docs.wso2.com/display/IS530/Authorization+Code+Grant
