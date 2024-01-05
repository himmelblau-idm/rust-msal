MSAL
====

The purpose of this project is to implement MSAL for Rust, based on the specifications found in the Microsoft API Reference for [ClientApplication Class](https://learn.microsoft.com/en-us/python/api/msal/msal.application.clientapplication?view=msal-py-latest) and [PublicClientApplication Class](https://learn.microsoft.com/en-us/python/api/msal/msal.application.publicclientapplication?view=msal-py-latest). These are Python references which will be mimicked in Rust here.

> **_NOTE:_**  Implementing the [ConfidentialClientApplication Class](https://learn.microsoft.com/en-us/python/api/msal/msal.application.confidentialclientapplication?view=msal-py-latest) is not currently a target for this project. If you are interested in volunteering to implement the ConfidentialClientApplication Class, please contact the maintainer.

In addition to the ClientApplication Class implementations, a goal of this project will also be to implement [MS-OAPXBC] sections [3.1.5.1.2 Request for Primary Refresh Token](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-oapxbc/d32d5cd0-05d4-4ec2-8bcc-ac29ce711c23) and [3.1.5.1.3 Exchange Primary Refresh Token for Access Token](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-oapxbc/06e2bf0d-8cea-4b11-ad78-d212330ebda9). These are not implemented in Microsoft's MSAL libraries, but are possible when authenticating from a [joined device](https://github.com/himmelblau-idm/himmelblau-device-enrollment).

How do I use this library?
--------------------------

Import the module into your project, then include the PublicClientApplication:

```Rust
use msal::PublicClientApplication;
```

Create an instance of the PublicClientApplication, then authenticate:

```Rust
let authority_host = "login.microsoftonline.com";
let app = PublicClientApplication(client_id, tenant_id, &authority_host);
let scope = vec![];
let token = app.acquire_token_by_username_password(username, password, scope)?;
```

You can obtain your `client_id` and `tenant_id` from the Azure portal.

You can perform a silent auth using a previously obtained refresh token:

```Rust
let token = app.acquire_token_silent(scope, &token.refresh_token)?;
```

Or finally, you can perform a Device Authorization Grant:

```Rust
let flow = app.initiate_device_flow(scope)?;

// Prompt the user with the message found in flow.message

let token = app.acquire_token_by_device_flow(flow)?;
```