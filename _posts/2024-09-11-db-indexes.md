---
layout: post
category: Fundamentals
title: "DB Indexes (Indices?)"
---

###### How indexes work under the hood

A db index is essentially just a new, separate table with two columns: the value in the indexed column, and a pointer to the full row in the real db table. And crucially, the index is ordered and stored as a binary tree, making lookups to it O(log(n)) instead of O(n). So when I have the following in my schema:
```
create_table "table_rows", force: :cascade do |t|
  t.integer "position"
  t.string "team_name"
  t.integer "points"
  t.integer "goal_difference"
  t.bigint "table_id", null: false
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
  t.float "xg_diff_per90"
  t.bigint "club_id"
  t.index ["club_id"], name: "index_table_rows_on_club_id"
  t.index ["table_id"], name: "index_table_rows_on_table_id"
end
```

The index at the end means we're creating two additional little tables, and the first of those should look like:
```
club_id     |  reference
2345982345  |  <pointer to table row>
9834589234  |  <pointer to table row>
3347239502  |  <pointer to table row>
```

###### Why use indexes?

They make table lookups faster because of how the data is stored (in a b tree).

As a place to start, indexing on foreign key columns makes sense. Why? Let's take the example above, and look at the `table_id` index. We know that in order to display a table, we want to pull all table rows that have the `table_id` of that particular table, and that's something we'll want to do fairly frequently. Having an index there makes the lookups faster, and makes the platform faster. 

Unique indexes provide another reason to use db indexes: they enforce a constraint, and ensure that records that would violate that constraint do not make it to the database. See 'Current task and logic' below for an example of this. These constraints can serve as a backup for regular Rails validations.

###### When not to use indexes

Db indexes are often useful for making lookups faster, but there are some situations not to use them in.

If a particular table deals with lots of update actions, needing to update the index as a part of each update is inefficient. Adding an index to a small table can be inefficient too, as it may take as long to do a lookup on the index as on the table itself. And if a particular column has lots of null values, an index might just be taking up space, as it repeats 'null' over and over.

###### Current task and logic

In the db for my test app, I was getting duplicate match data, as I had indexed on a combination of the `date_time`, `home_team_name`, and `away_team_name` fields; this was based on the assumption that team names would be constant everywhere, which turned out to be incorrect. First, I took steps to delete the duplicates, and remove the useless index:

```
ids = Match.select("MIN(id) as id").group(:date_time,:home_team_id).collect(&:id)
Match.where.not(id: ids).destroy_all
ids = Match.select("MIN(id) as id").group(:date_time,:away_team_id).collect(&:id)
Match.where.not(id: ids).destroy_all
```
```
class RemoveIndexFromMatches < ActiveRecord::Migration[7.1]
  def change
    remove_index :matches, column: ["date_time", "home_team_name", "away_team_name"]
  end
end
```

Then, I decided to add (non-null) indexes, since a team should not be playing more than one time on the same date:

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

One additional piece this decision required: when a club is created, it needs to be associated with existing matches (where previously we only had the club name) before matches are pulled. That way, the index is updated with the newly created club, and we don't pull duplicate matches on a refresh. Problem solved.

###### References

<span class="list  list--md one">[https://www.simplilearn.com/tutorials/sql-tutorial/index-in-sql](https://www.simplilearn.com/tutorials/sql-tutorial/index-in-sql#:~:text=While%20indexes%20are%20designed%20to,large%20numbers%20of%20null%20values)</span>
<span class="list  list--md one">[https://planetscale.com/blog/how-do-database-indexes-work](https://planetscale.com/blog/how-do-database-indexes-work)</span>
<span class="list  list--md one">[https://builtin.com/data-science/b-tree-index](https://builtin.com/data-science/b-tree-index)</span>
