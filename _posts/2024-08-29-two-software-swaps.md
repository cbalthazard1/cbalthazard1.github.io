---
layout: post
category: "Ruby on Rails"
title: "Two Swaps"
---

In the course of working on my 'rails test app,' I decided to make two simultaneous software swaps: Sqlite3 to Postgres, and Sidekiq to GoodJob.

###### Sidekiq to GoodJob

This came about not because of any technical limitations, but because I wanted to try something new! A former colleague mentioned their team decided to use GoodJob for a project rather than Sidekiq, so I decided the same. It would be a good exercise to do the swap, and good to familiarize myself with a different job queueing technology.

As you can see in the [commit](https://github.com/cbalthazard1/rta/commit/d7c5bbd0a5b8cb3154f7a386d0168f69ff4815ae), the swap was very straightforward (the actual gem change took place [here](https://github.com/cbalthazard1/rta/commit/e7435d9745837067baf724b50005711a7b9dd949), though more on that below)! I'm excited to make use of the bulk enqueueing feature, which will certainly come in handy when updating all tables and clubs. 

###### Sqlite3 to Postgres

The impetus for this swap was simple: GoodJob requires Postgres (which you can see from the [GoodJob commit](https://github.com/cbalthazard1/rta/commit/d7c5bbd0a5b8cb3154f7a386d0168f69ff4815ae)).

Why Postgres? Sqlite is known for its exclusive locking on writes, which would limit the speed of the job queueing feature. Instead, Postgres advisory locking (more info [here](https://softwareengineeringwk.substack.com/p/a-practical-guide-to-using-advisory)) provides 'run-once safety' and creates reduced latency.

Sqlite3 is the database that comes with rails out-of-the-box. It's meant for lightweight apps and doesn't require a separate server to be running, which for most cases would fit into what I'm doing here, but this new dependency changes things. Additionally, postgres allows for a greater range of db variable data types, including jsonb (or better json (really binary)), which has better performance than regular json.

The key downside: the database I had built up was essentially wiped. That means I'll need to manually re-create clubs before pulling them, and test that their links to tables continue to work. On the plus side, this experience showed the importance of saving that db somehow, and I'm going to try out another new-to-me rails gem: [Seed Dump](https://github.com/rroblak/seed_dump). Though that's for another day.