﻿# OAuth2 #
[![Build status](https://ci.appveyor.com/api/projects/status/391rgw9mf4qnolio/branch/master?svg=true)](https://ci.appveyor.com/project/niemyjski/oauth2/branch/master)
[![NuGet Version](http://img.shields.io/nuget/v/OAuth2.svg?style=flat)](https://www.nuget.org/packages/OAuth2/)

OAuth2 is a library for user authentication using third-party services (OAuth/OAuth2 protocol) such as Google, Facebook and so on.

## Current Version and Status ##

Current version is 0.9. Status is "release candidate" - despite we already have several real life projects built with usage of this library, we will change status to "stable" only after comprehensive set of acceptance tests is ready and runs successfully.

## Standard Flow ##

Following are the steps of standard flow:

- generate login URL and render page with it
- define callback which will be called by third-party service on successful authentication
- retrieve user info on callback from third-party service

## Usage Example ##

Several simple steps to plug in the library into your app:

Install OAuth2 package via [NuGet](http://www.nuget.org/packages/OAuth2/)

```shell
Install-Package OAuth2
```

Your action function that needs to generate and redirect to the third-party service will look like

```c#

public ActionResult GoogleLogin()
{
  var redirectUri = new Uri(Url.Action("GoogleLoginCallBack", "Account", null, protocol: Request.Url.Scheme));
  var googleClient = new GoogleClient(new RequestFactory(), new OAuth2.Configuration.ClientConfiguration
  {
    ClientId = auth.ClientId?.Trim(),
    ClientSecret = auth.ClientSecret?.Trim(),
    RedirectUri = redirectUrl,
    Scope = "profile email"
  });
  return Redirect(googleClient.GetLoginLinkUri("SomeStateValueYouWantToUse"));
}
```

Finally the action that handles the callback will look like this :

```c#

public ActionResult GoogleLoginCallBack()
{
  var code = Request.QueryString["code"];
  var redirectUri = new Uri(Url.Action("GoogleLoginCallBack", "Account", null, protocol: Request.Url.Scheme));
  var googleClient = new GoogleClient(new RequestFactory(), new OAuth2.Configuration.ClientConfiguration
  {
    ClientId = auth.ClientId?.Trim(),
    ClientSecret = auth.ClientSecret?.Trim(),
    RedirectUri = redirectUrl,
    Scope = "profile email"
  });
  
  try
  {
    userInfo = oauth.GetUserInfo(new NameValueCollection() { { "code", code } });
  }
  catch(Exception ex)
  {
    return RedirectToAction("LoginError", new {error = ex.Message});
  }
  
  // do your validation and allow the user to proceed
  if (SignInManager.IsUserValid(userInfo.Email))
  {
    SignInManager.Login(userInfo.Email);
    return RedirectToAction("Index", "Home", new {error = ex.Message});
  }
  return Redirect(googleClient"LoginError", new {error = "User does not exists in the system"});
}
```

## Supported Services ##

- Asana
- DigitalOcean
- Facebook
- Foursquare
- GitHub
- Google
- Instagram
- LinkedIn
- MailRu
- Odnoklassniki
- Salesforce
- Spotify
- Todoist
- Twitter
- VK (Vkontakte)
- Visual Studio Team Services (VSTS)
- Windows Live
- Yahoo
- Yandex
- Uber

## Goals ##

Before I started working on this project I considered available solutions: several ones were found, but I wasn't satisfied with results:

- some of them were too complex for such simple task as authentication via OAuth2
- some - didn't have usage examples or documentation

So, I decided to implement this library striving to achieve following goals:

- simplicity in usage - so even newbie can just call couple of methods and receive expected results
- well-documented, testable and tested (!) code - current coverage (according to NCrunch) is greater than 80%, several acceptance tests are also implemented (SpecFlow + WatiN)
- flexible, transparent and easily understandable design, so library can be used both by people who need only certain parts and fine-grained control over them and by people who want just plug it in and immediately receive expected result
- self-education :) - it was interesting to see how OAuth2 works

## Dependencies ##

This library is dependent on:

- RestSharp (http://restsharp.org/)
- Newtonsoft.Json (http://json.codeplex.com/)

## Contributors ##

- Constantin Titarenko (started development, defined library structure, released initial version)
- Blake Niemyjski (helped a lot to maintain the project, currently (since 2015) - top maintainer)
- Andrew Semack (helped a lot with improvements on configuration as well as with extending list of supported services by implementing their clients)
- Sascha Kiefer (simplified extending library with own provider implementations, added GitHub client)
- Krisztián Pócza (added LinkedIn (OAuth 2) client)
- [Jamie Houston](/JamieHouston) (added a [Todoist client](/tree/master/OAuth2/Client/Impl/TodoistClient.cs))
- [Sasidhar Kasturi](https://github.com/skasturi) (added Uber, Spotify, Yahoo)
- [Jamie Dalton](https://github.com/daltskin) (added Visual Studio Team Services)

## Acknowledgements ##

Many thanks to [JetBrains](http://www.jetbrains.com/) company for providing free OSS licenses
for [**ReSharper**](http://www.jetbrains.com/resharper/) and [**dotCover**](http://www.jetbrains.com/dotcover/) -
these tools allow us to work on this project with pleasure!

Also we glad to have opportunity to use free [**Teamcity**](http://www.jetbrains.com/teamcity/) CI server
provided by [Codebetter.com](http://codebetter.com/) and [JetBrains](http://www.jetbrains.com/) -
many thanks for supporting OSS!

![JetBrains](http://www.jetbrains.com/img/banners/Codebetter300x250.png)

OAuth2 optimization would never be so simple without YourKit .NET profiler!
We appreciate kind support of open source projects by YourKit LLC -
the creator of innovative and intelligent tools for profiling .NET [**YourKit .NET Profiler**](http://www.yourkit.com/.net/profiler/index.jsp)
and Java applications [YourKit Java Profiler](http://www.yourkit.com/java/profiler/index.jsp).

## Roadmap ##

- Implement more acceptance tests
- Increase code coverage by finalizing unit tests

## License ##

The MIT License (MIT)
Copyright (c) 2012-2013 Constantin Titarenko, Andrew Semack and others

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
