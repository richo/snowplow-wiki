This is a page of hints, tips and explanations to help you work with SnowPlow. If something looks like a bug in SnowPlow but isn't, it will end up on this page too.

### Why are browser features all recorded as null for Internet Explorer?

With the exceptions of cookies and Java, our JavaScript tracker cannot detect what browser features (PDF, Flash etc) a given instance of Internet Explorer has. This is because IE, unlike the other major browsers, does not populate the `window.navigator.mimeTypes[]` and `navigator.plugins[]` properties. There are other ways of detecting some browser features (via ActiveX), but these are not advised as they can trigger UAC warnings on Windows.