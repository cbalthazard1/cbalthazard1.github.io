---
layout: post
category: Ruby
title: "API endpoints in Ruby"
---

In [the previous article](https://balthazard.dev/fundamentals/2024/06/26/API-endpoints/), we went through the main pieces of the API-calling puzzle. Now, we use those pieces to actually make some API calls (you can do this in irb).

###### POST example
Let's use a no-auth API to keep things simple to start: [https://api.reporter.nih.gov/](https://api.reporter.nih.gov/)

We're going to use the [Net/HTTP gem](https://docs.ruby-lang.org/en/3.0/Net/HTTP.html), as well as json, so at the top of our file we should have:
```
require 'net/http'
require 'uri'
require 'json'
```

First let's set up our URI object:
```
uri = URI.parse('https://api.reporter.nih.gov/v2/projects/search')
```
Next, we should set our request body, to only get data for a certain professor at a certain school:
```
request_body = {
  "criteria": {
    "org_names": [
      "Georgetown University"
    ],
    "pi_names": [
        {
        "any_name": "balthazard",
        "first_name": ""
        }
    ]
  },
  "offset": 0,
  "limit": 10,
  "sort_field":"project_start_date",
  "sort_order":"desc"
}
```
Finally, our headers:
```
headers = { "Content-Type": "application/json", "accept": "application/json" }
```
Then we can send the POST request with the body (in json format) and headers to the right URI:
```
result = Net::HTTP.post(uri, request_body.to_json, headers)
```
And call `result.body` to get the result's body. That's it! However, since I only went to school at Georgetown, and didn't work there, the result will be empty.

You can also call `.code` or `.message` on result to get more info about the response.

###### GET example

If instead we wanted a GET request, we would update the call to be `result = Net::HTTP.get_response(uri)`. In the case of the previous example, the [API documentation](https://api.reporter.nih.gov/) tells us that a POST is needed, not a GET (which makes sense, since we're sending along a request body as well).

We can use a different (still no-auth) example instead: [https://api.artic.edu/docs/#introduction](https://api.artic.edu/docs/#introduction). In the documentation, it seems we can get data for just one artwork with: `https://api.artic.edu/api/v1/artworks/129884`

So assuming we already have the require statements in place, we can set up the URI object with:
```
uri = URI.parse('https://api.artic.edu/api/v1/artworks/129884')
```
And then just run this to get the data:
```
result = Net::HTTP.get_response(uri)
```

###### DELETE example

On second thought, let's not go deleting free API resources. Probably wouldn't have the access anyway.

###### Auth example

We've avoided doing authorization so far, and it's been nice to simplify things, but we should run through an example where auth is needed. Let's use [https://the-odds-api.com/liveapi/guides/v4/#overview](https://the-odds-api.com/liveapi/guides/v4/#overview) (sports betting is... not the best, but it can provide us some interesting info). Pretty straightforward to get an auth token by email and use it in the following steps.
```
uri = URI.parse('https://api.the-odds-api.com/v4/sports?apiKey=XXXXX')
result = Net::HTTP.get_response(uri)
result.body
```
This works, and is a valid path to take! Alternatively, to go through a GET with params not included directly in the URI object, we can do the following:
```
uri = URI.parse('https://api.the-odds-api.com/v4/sports')
params = { apiKey: '481187b53f2da2bbfb4b9dde490d4b7f' }
uri.query = URI.encode_www_form(params)
result = Net::HTTP.get_response(uri)
result.body
```
