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
* IdentityServer is used as a middleware in our services pipeline
