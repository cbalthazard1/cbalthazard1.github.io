---
layout: post
category: Fundamentals
title: "API Endpoints"
---

I’ve accessed data via APIs fairly frequently, but have not dived into the details of how those calls work. So let’s do that here.

APIs (Application Programming Interface) can generally be SOAP or REST (REpresentational State Transfer) - let’s focus on REST. There’s plenty to say about when and why to use APIs in making data available, but I want to look more at how to use them. What data needs to be sent (this article), and how do we structure that in Ruby ([the next article]((https://balthazard.dev/ruby/2024/06/27/API-endoints-in-ruby/)))?

###### Methods

These are your typical CRUD (create, read, update, delete) methods: PUT, POST, GET, and DELETE. We’ll ignore PATCH. DELETE is a delete, GET is a read (and does not include a request body). 

PUT either creates or updates a resource at a particular URI (Uniform Resource Identifier), specified in the request body (it refers to a singleton resource). It is idempotent, so you can run the same PUT multiple times and the requests after the first one will not change anything.

POST either creates or updates a resource for a collection URI, and is not idempotent. In practice, POST is usually used as a create, and PUT as an update.

###### Parameters

Options that can be passed with the request which may change the result. These are analogous to url params. They are often filters; pagination can also be implemented here with some combination of offset, limit, page, and page number. 

###### HTTP (request) headers

Key-value pairs that are sent along with the request. More metadata than parameters; however, headers, parameters, and body can flow together a bit. Some common examples:\
<span class="list  list--md one"><strong>Authorization</strong>. Can come in several flavors (chosen by the API provider):</span>
	<span class="list  list--md two">None! Concerning. It makes sense in some cases, though. Don’t include the key at all in this case.</span>
	<span class="list  list--md two">Basic. Username and password concatenated together with a `:`, encoded base 64. This ends up looking like: `Authorization: Basic #{encoded_username_password}`</span>
	<span class="list  list--md two">API key. The API provider provides (you’re here for the writing, ultimately) a unique API key on request. Looks like: `Authorization: api-key #{api_key}`</span>
	<span class="list  list--md two">OAuth 2.0 (1.0 is legacy). Uses a client id and secret with one more auth step before getting a token, which is used in the form: `Authorization: Bearer #{token}` A [good example](https://docs.informatica.com/integration-cloud/api-manager/current-version/api-manager-guide/authentication-and-authorization/oauth-2-0-authentication-and-authorization/python-3-example--invoke-a-managed-api-with-oauth-2-0-authentica.html) of getting the token.</span>
	<span class="list  list--md two">JWT (Json Web Token). Similarly, needs an extra step before getting to the Bearer token.</span>
<span class="list  list--md one"><strong>Content-Type</strong>. Usual values are `application/json` or `text/json`. Indicates the media type of the response. A [good example](https://www.geeksforgeeks.org/http-headers-content-type/) of why including this is important.</span>
<span class="list  list--md one"><strong>Accept</strong>. More info [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept). I’ve had this match `Content-Type` previously.</span>

###### Request body

The data used in creating, updating, or deleting a resource. Can also be used to narrow down results, functioning similar to parameters. If you’re using an API to add a blog post, the text of the post would go here.

###### Sources/further reading

[https://blog.postman.com/what-are-http-methods/](https://blog.postman.com/what-are-http-methods/) \
[https://blog.postman.com/what-is-an-api-endpoint/](https://blog.postman.com/what-is-an-api-endpoint/) \
[https://restfulapi.net/rest-put-vs-post/](https://restfulapi.net/rest-put-vs-post/) \
[https://www.merge.dev/blog/rest-api-authentication](https://www.merge.dev/blog/rest-api-authentication) \
[https://auth0.com/blog/why-migrate-from-api-keys-to-oauth2-access-tokens/](https://auth0.com/blog/why-migrate-from-api-keys-to-oauth2-access-tokens/)

<strong>UPDATE 6/30/24: for implementation details in Ruby, [the next article is now available](https://balthazard.dev/ruby/2024/06/30/API-endoints-in-ruby/).</strong>