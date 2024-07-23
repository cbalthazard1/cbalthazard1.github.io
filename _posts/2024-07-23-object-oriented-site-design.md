---
layout: post
category: Website
title: "Object-Oriented Site Design"
---

I've done some work ([part 1](https://balthazard.dev/ruby/2024/07/15/new-rails-test-app-part-1/), [part 2](https://balthazard.dev/ruby/2024/07/16/new-rails-test-app-part-2/)) to set up a simple test rails app, but it's time to expand its functionality a bit to actually make it useful. We'll do that by thinking through user-centered design and domain driven design (DDD).

###### Goals of the app

Pun intended! The goal is to provide a single location for aggregated statistics from various sources. I want a user to be able to look at a league table and see quickly who is outperforming their xG, and how a club's ELO rating has fluctuated over time. Seeing all upcoming games across leagues would be useful, as would a clear visual of the financial inequities in given leagues and fixtures.

The site should be refreshed daily with a clock job, and there should be a button that refreshes all data manually. A more granular level doesn't make sense since all the data is interrelated.

###### DDD

Right now, tables and table rows (both from FBRef) are the building blocks of the site. Instead, clubs should be the building blocks (source agnostic). However, clubs do not belong to leagues consistently, so we will want to make sure we don't hard-code that link, but rather do a lookup based on country.

We will also need to keep in mind the league/country divide as an edge case, since Swansea City (Wales) play in the English Championship.

###### Breaking down steps to take based on design plan

At some point we will build an exporter to export club-specific info in addition to table info. So for each exporter, we'll need both source and type available. So `table_exporter` can be one, and `club_exporter` can be another, nested in an `fbref` folder, with appropriate configurations nested underneath. For now, a data refresh from the homepage will export all table data, and from each table page will just export that table's data (this is already mostly implemented).

The main new piece we need for now is to build up club data from the table exports. Clubs will need configuration based on site (for FBRef, for example, name, country_abbr, and url). So the club's model should consist of: name, location, country, config (config is nested site-by-site data when exporting just a club, or when exporting tables where we expect to find club data).

Then how do we populate the club's data from the exported table data? We'd be adding far too much logic to the controller, which should be kept as minimal as possible. So let's implement a [service object](https://www.codeproject.com/Articles/5340675/Using-Rails-Service-Objects-to-Keep-Code-Clean), to continue practicing that abstraction structure.

###### Some challenges

How to handle looking up the nested `fbref_table_name` key-value pair in the `config` db column. I don't love it, but the scale of things at the moment means we should be able to handle [this solution](https://stackoverflow.com/questions/64759460/rails-accessing-a-models-nested-hash-value-in-a-where-clause) not being the most efficient.

Interesting syntax thing on [conditional link rendering](https://til.hashrocket.com/posts/2kueksf4xd-conditionally-render-rails-links-with-linktoif).

On the other hand, the table/club split is simplifying things so far. The tables controller `#refresh_all` method is refreshingly straightforward:
```
def refresh_all
    Table.all.each do |table|
      table_refresh_service.refresh_table(table[:id])
    end

    redirect_to root_path
end
```

###### Progress update


Everything pictured is working (along with table sorting)!

![image](/assets/images/rta_site_design_1.png)
![image](/assets/images/rta_site_design_2.png)
