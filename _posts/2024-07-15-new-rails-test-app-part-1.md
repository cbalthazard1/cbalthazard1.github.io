---
layout: post
category: "Ruby on Rails"
title: "New Rails Test App (Part 1)"
---

Time to set up a new rails app. I've mainly followed the steps [here](https://guides.rubyonrails.org/getting_started.html), but with an eye towards adding infrastructure to pull information about soccer league tables using jobs. I'll add some info about decision points here.

You can see progress on this project [on GitHub](https://github.com/cbalthazard1/rta). I'm just running things locally, so I can work more quickly and work off main.

###### DB Schema

This ended up being pretty simple. For now, I want to display Tables, and each of those tables has many TableRows. I left some update/delete stuff in there on the frontend for now, but may delete later. Not sure yet how I'm going to go about scraping/hitting API endpoints to get the data, but planning to get the jobs going first to build out the data pipeline, getting table info into the database.

###### Sidekiq

[Jobs](https://guides.rubyonrails.org/active_job_basics.html)! It's necessary at the moment to run `bin/rails s` and `redis-server` to get the `http://localhost:3000/sidekiq/queues` page running locally. Then enqueuing a job in a console, and running `bundle exec sidekiq` executes that job. Can we simplify this? Yes - using [a new Rails 7 feature](https://railsnotes.xyz/blog/procfile-bin-dev-rails7) means we can simplify things to just `bin/dev` to run all three pieces, with a `Procfile.dev` specifying how to run each process. Here's that Procfile (for now):
```
web: bin/rails server -p 3000
redis: redis-server
sidekiq: bundle exec sidekiq
```

###### Exporters

I used nokogiri to handle parsing the scraped HTML file from FBRef. Hard-coded to start and then extended functionality to be more general. One decision point was whether to iterate over all the Nodes in tbody, but I decided against that because we want to specify the keys in defining the data structure. Could end up making this a Value object but it's a hash for now. Parsing is also taking place here since it's so straightforward for now, but I'm open to changing this later.

Pausing here now to get this post out, but more to come in part 2! A glimpse at the (very, very basic) visual step we're at now, where we can pull data from a site, parse it in nokogiri, save it in the db, and display it:

![image](/assets/images/rta_part_1.png)