---
layout: post
category: "Ruby on Rails"
title: "Rails Syntax Odds and Ends"
---

In continuing to build out the data model for my 'rails test app', I've been noticing a few syntax tidbits it's worth keeping in mind.

###### References

I've spent the last week or so building out matches. Matches take place between two clubs, which may not exist in the app yet, so we'll want to separately store the club name. Seems odd that they might not exist - but if I'm starting out populating major European leagues, pulling in a Champions League game against Red Star Belgrade shouldn't error out, but there shouldn't be a link to the team on the match list yet.

We're looking to reference a table twice, which means we can't use the rails behind the scenes magic of just using the right name and say `t.references :club`.

The [official reference page](https://guides.rubyonrails.org/active_record_migrations.html#references) was not the most useful on this topic, but [this Stack Overflow link](https://stackoverflow.com/questions/31936481/how-to-add-foreign-key-in-rails-migration-with-different-table-name) clarified the syntax. Fortunately, I had already set up the [associations](https://guides.rubyonrails.org/association_basics.html) to work logically between matches, tables, clubs, and table rows. The updated code in the migration file:
```
t.references :home_team, foreign_key: { to_table: :clubs }, index: true
t.references :away_team, foreign_key: { to_table: :clubs }, index: true
```

As an aside, I also set an index on date, home team name, and away team name, both for validation that we get unique values of combinations there, and so we get a quick lookup for that validation.
```
add_index :matches, [:date_time, :home_team_name, :away_team_name], unique: true
```

###### Validating boolean value

I was briefly getting unexpected results testing for Manchester City, where the only results coming in were from the Super Cup against Sevilla, the FA cup semis, and then the FA cup final. The commonality? All were at neutral venues. So I needed to change the validation for that column from presence to [inclusion](https://guides.rubyonrails.org/active_record_validations.html#presence), from the first line here to the second:
```
validates :neutral_site, inclusion: [true, false]
```

###### A note on naming

The first object I pulled in was a Table, so I named the first db table "Table". If I could go back a few weeks, I might do more work to figure out the purpose/overall design of the app before doing naming, as making an update now isn't quite worth the time. "Table" should really be "League" or "Competition", but I can make that change in the frontend where needed and leave the "Table" table as-is.