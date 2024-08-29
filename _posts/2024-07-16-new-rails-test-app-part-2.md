---
layout: post
category: "Ruby on Rails"
title: "New Rails Test App (Part 2)"
---

Rails test app continued! My next steps are to prioritize what needs updating - and it's clearly the hardcoded values in both the API exporter file and the update tables job file. By the end of this article, I should have the ability to export another table without custom code beyond the FBRef url.

###### Generalizing to other tables

Four key pieces here: update `table: Table.find(1)` in the job to be generalized; pass args through from the job to service to API calls; fix xpath searching to not need as many specifics; create config structure to hold needed information for each table.

What's that necessary information to start? It looks to me like we just need the URL (as the db table will be passed through). Should we put that in a new db column `url`, or a new column `config` where we can continue to add to that hash as-needed? This is the key decision point in this article: how does the structure of the data (and the intended future of the website) dictate the way the app is built?

###### Configuration decision

The db table is called 'tables' - do we assume that each table is coming from an FBRef exporter? I think the answer to that has to be no, since that site could shut down and we'd need to pull data from another source. So, do we want to add a new db column `fbref_url`, and then a separate db column for each subsequent necessary piece of configuration data? Definitely not. Configuration hash it is, then (Quick aside: took a look and I am comfortable with country_abbr, level, and name remaining the top-level data pieces in that table).

###### Parameters

On the update page, we want to display not the config hash in its entirety, but just the fbref_url value. We also want to be able to save the fbref_url not as the entire config hash, but in the right place. A [bit of syntax stuff on strong parameters](https://joshfrankel.me/blog/modifying-strong-parameter-values-after-a-request/) was needed here.

And in order to iterate through the config hash and display elements, as well as on initial display with the new action, we need the following erb template code:
```
<% @table.config.keys.each do |key| %>
  <div class="field">
    <%= form.label key %><br />
    <%= text_area_tag "table[" + key + "]", @table.config[key], size: "20x5" %> 
  </div>
<% end %>

<% if @table.config.keys.size==0 %>
  <div>
    <%= form.label :fbref_url %><br>
    <%= form.text_area :fbref_url %>
    <% table.errors.full_messages_for(:fbref_url).each do |message| %>
      <div><%= message %></div>
    <% end %>
  </div>
<% end %>
```

###### Refresh

We can now create a table in the UI and manually run the update tables job to populate data (I tested this with Serie A). We should add a button to run the job instead, passing through the correct table number.

I used some more [references](https://guides.rubyonrails.org/routing.html#adding-member-routes) on Rails routing for the refresh action, and managed to get things working in a link at the bottom of the page. One hiccup with page reload (which shouldn't happen on click, but after the job is done) - I ended up solving that by adding `sleep(0.5)` to the controller. Frankly, might be better without using async jobs. But ultimately, at the end of this article, all a user needs is the FBRef url to get data pulling into this site. Good progress!

Final note: one crazy thing happened! I noticed the refresh job was running when my mouse was only hovering over the Refresh button [(!!)](https://www.reddit.com/r/rails/comments/1b9mr1a/link_to_is_sending_get_request_on_hover/). Needed to modify the html to disable Turbo prefetching links. Ugh.
