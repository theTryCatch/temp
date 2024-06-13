Hi,

Sorry for the delay; I've been busy with illness and work. I'll keep this brief.

Webstax is a key tool in our MS stack. The latest version, Webstack2, is a rebranded successor to Webstack1, which is now obsolete.

The APIs I shared with you are deployed using the Webstax pattern. Unlike Webstack1, Webstax doesn't yet offer granular control over request flow. As a result, you might encounter CORS issues with these APIs.

As we discussed, once we create a Webstax application with BYO_OIDC of our parent OIDC registration, these issues should be resolved after deployment.

To keep development smooth, we have the following options:

1. Use mocked data for development.
2. Use the iFrame tag carefully.
3. Open the API endpoint in another tab of the same browser and refresh it every hour. This way, the authorization code details will be retained in the browser cookies.

Regardless of your choice, I will look into allowing all request headers and sending responses with additional headers in a low-priority mode.

You might have these questions:

1. Why stick with Webstax despite complications?
   - We can't create new .NET projects using .NET Framework 4.x. The minimum version is .NET 8. Webstax is the nearest and most affordable hosting environment for .NET apps.

2. Why .NET and not another language?
   - We mainly work with Windows OS, so .NET is the best framework to avoid the hassle of using Java or another language.

3. How do you develop your applications?
   - I run all my APIs locally on my development machine.

4. Can I run those APIs locally too?
   - Yes, if you know how to start the build and have a good compute-capable VDI or moonshot.

5. What are the future plans?
   - Starting next week, I will work on designing a gateway API for routing and non-business logic. This doesn't need to be a .NET application, so I plan to implement it in Python and host it in Webform+ as an FCGI application. This should hopefully avoid CORS issues, but we'll see.

Hope this clears things up!

Best,
[Your Name]
