---
layout: post
category: Fundamentals
title: "DB Indices"
---

In continuing to build out the data model for my 'rails test app', I've been noticing a few syntax tidbits it's worth keeping in mind.

###### Why use indices?

General stuff

###### How indices work under the hood

Explainer

###### Current task and logic
```
ids = Match.select("MIN(id) as id").group(:date_time,:home_team_id).collect(&:id)
Match.where.not(id: ids).destroy_all
ids = Match.select("MIN(id) as id").group(:date_time,:away_team_id).collect(&:id)
Match.where.not(id: ids).destroy_all
```
```
t.index ["date_time", "away_team_id"], name: "index_matches_on_date_time_and_away_team_id", unique: true, where: "away_team_id IS NOT NULL"
t.index ["date_time", "home_team_id"], name: "index_matches_on_date_time_and_home_team_id", unique: true, where: "home_team_id IS NOT NULL"
```
```
class RemoveIndexFromMatches < ActiveRecord::Migration[7.1]
  def change
    remove_index :matches, column: ["date_time", "home_team_name", "away_team_name"]
  end
end
```

```
class AddIndexToMatches < ActiveRecord::Migration[7.1]
  def change
    add_index :matches, [:date_time, :home_team_id], unique: true
    add_index :matches, [:date_time, :away_team_id], unique: true
  end
end
```