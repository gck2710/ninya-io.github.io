---
title: "How we handle server side rendering with our AngularJS app"
date: "2014-05-23"
categories:
    - "ninya.io"
---

Disclaimer: That's might be a bold title for the technique described in this post. We hope you don't
hate us by the end of the post ;-)

### What do we need serverside rendering for?

Let's take a look at how [ninya.io](http://www.ninya.io) handled shared queries until today. What is a shared query anyway?

http://www.ninya.io/?q=location:%20Berlin%20answers:%20javascript,%20git

[ninya.io](http://www.ninya.io) let's you share queries with a simple query parameter syntax. There's a twitter share
button on top the results that produces such a link to make things easy.

![share button](/how-we-handle-server-side-rendering-with-our-angularjs-app/share_button.png)

### What happens when you open such a link?

[ninya.io](http://www.ninya.io) is a single page application (SPA) written with AngularJS. The idiomatic SPA way
is to evaluate the query parameters on the client side *in the browser* and then fetch the
results via ajax.

Unfortunately this approach has two major drawbacks.

### Speed.

It's pretty obvious: If the browser first has to fetch the app from the server and then has to fetch the
JSON data with a subsequent request, you are ruining performance for your users.

This is the main reason why airbnb and twitter switched to a more sophisticated approach where the
SPA is run on the server to construct the html for the initial response. After that the app runs just
like any other SPA and fetches JSON from a server via ajax to construct html on the client side.

### SEO

But it's not only speed we are sacrificing. If the first response does not include the entire html, search engines
can't see it. Well, there are other techniques to work around this and after all Google said they are
[starting to look at web pages the way humans see them](http://googlewebmastercentral.blogspot.de/2014/05/understanding-web-pages-better.html)
but if we can do better, we should.

So, let's do what airbnb and twitter do and include the entire html for the result set with the initial response!

Unfortunately there's no out of the box support for server side rendering in AngularJS. Although it has been [under discussion](https://github.com/angular/angular.js/issues/2104)
for quite some time among the AngularJS community.

### How does ninya.io do it?

The way [ninya.io](http://www.ninya.io) handles this is far less sophisticated compared to how airbnb or twitter handle it. It only
works for us because our frontend code base is very small. This approach probably does not scale very well
for larger code bases.

1. We have our entire html in one file. There are no templates loaded via `ng-include`, `ng-view` or something alike.

2. The only critical part for us is constructing the result list which is rendered as an `<ul>`. AngularJS constructs
the `<li>` elements on the client side via a `ng-repeat` attribute.

![client side list](/how-we-handle-server-side-rendering-with-our-angularjs-app/client_list.png)

What we do is, we duplicate this peace of markup and construct the `<li>` part via server side templating (we use ejs).
Yes, that's right - we sacrifice DRY here. We end up with two lists but only one of them is visible at the same time.

![server side list](/how-we-handle-server-side-rendering-with-our-angularjs-app/server_list.png)

3. Now the next thing we need is a way to tell our AngularJS app whether it should display the angularized `<ul>` or the
server constructed `<ul>`. We simply inject a global variable `var serverRendered = true` into our app that our AngularJS
code has access to.

![injected variable](/how-we-handle-server-side-rendering-with-our-angularjs-app/serverRendered.png)

Based on that state, AngularJS can toggle visibility between the lists.

When the user starts typing into the search boxes we set it to `false` so that the server side generated `<ul>`
becomes hidden and the other one becomes visible.

Again, it's a simple approach and won't work for bigger apps. It serves our use case well though.
