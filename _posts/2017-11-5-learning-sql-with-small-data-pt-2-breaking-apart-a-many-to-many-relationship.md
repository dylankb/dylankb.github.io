---
layout: post
title: "Learning SQL With Small Data Pt 2: Taking Apart a Many-to-Many Relationship"
comments: true
---

_If you haven't yet, read [part 1]({{ site.url }}{% link _posts/2017-10-27-learning-sql-with-small-data-pt-1-counting-with-joins-and-subqueries.md %}) which looks at counting things with JOINs and subqueries_

We'll continue working with the fake library data we've been using, but now we'll focus on a couple new tables that deal with the relationship between two entities: users and books. Users and books share a "many-to-many" relationship; users can have many books and books can have many users.

There are three tables that represent this relationship: `users`, `books`, and `users_books`. When working with a Many-to-Many relationship you set up a third table, known as the join table, which provides data on the relationship between the two main tables (`users` and `books`). In this case `users_books`, I think about the table as representing the data for "checkouts" that happen at a library. Therefore at a minimum a join table row (i.e. one "book checkout") will need to include data that identifies one user and one book. A join table row does this by including at a minimum two columns: one column from each of the two main tables to serve as identifying keys. Because keys from one table now also reside on another table, they're referred to as _foreign keys_. Here's an entity relation diagram to help illustrate the relationship we just described between the `users` and `books` tables. The letter P represents the primary keys and the letter F represents foreign keys.

![](/assets/users-books.png)

SQL is a large topic, so it will be a bit easier to get started by identifying a specific question and then trying to answer it. Let's try to create a relatively straightforward query to retrieve the records of each book checkout that happened at the library. The data for a book checkout should include the user's name that checked out the book and the book's title.

Here's a query on a Many-to-Many relationship that's similar to one found in the LaunchSchool book's JOIN section.

```sql
SELECT users.username, books.title
  FROM users
 INNER JOIN users_books ON (users_books.user_id = users.id)
 INNER JOIN books ON (books.id = users_books.book_id);
```
```
username    |       title        
------------+--------------------
John Smith  | My First SQL book
John Smith  | My Second SQL book
Jane Smiley | My Second SQL book
```

... but I didn't really understand *how* JOINs on Many-to-Many relations worked, so I tried switching the order of the joins to see what happened.

```sql
SELECT users.username, books.title
  FROM users
 INNER JOIN books ON (books.id = users_books.book_id) /* Switched order */
 INNER JOIN users_books ON (users_books.user_id = users.id);
```
```
ERROR:  missing FROM-clause entry for table "users_books"
LINE 3: INNER JOIN books ON (books.id = users_books.book_id)
```

Annnnnd I broke it.

When you break something and don't know why, that's a clear indication that you have some gaps in your knowledge. What helped me finally get a strong grasp of how this type of join worked was to use some of the tools we've been practicing: displaying the data available to us, checking how this data can change, and using a small data set to do so.

So let's start building up this query that broke, starting with one of the simplest statements we can execute.

```sql
/* Adding additional info to SELECT to show more data */
SELECT u.id, u.username
  FROM users u
```

`FROM users` brings in the data displayed in our results.

 id |  username   
----|-------------
 1 | John Smith  |
 2 | Jane Smiley |
 3 | Alice Munro |

Ok, now that we've got some users, we just need query for books, right? Without considering how you query a Many-to-Many relation, you might indeed think this and go straight to writing this join next.

```sql
INNER JOIN books ON (books.id = users_books.book_id)
```

Here's the whole query so far.

```sql
SELECT u.id, u.username
  FROM users u INNER JOIN books ON (books.id = users_books.book_id);
```

Let's run it.

```
ERROR:  missing FROM-clause entry for table "users_books"
LINE 3: INNER JOIN books ON (books.id = users_books.book_id)
```

Hmm, looks like we don't have access to the `users_books` table. It's hard to tell if we retrieved `books` rows in our result table in our last query since it threw an error, so let's try another similar one.

```sql
SELECT u.id, u.username, books.title
  FROM users u
 INNER JOIN books ON (books.id IS NOT NULL);
```
```
id |  username   |       title        
----+-------------+--------------------
  1 | John Smith  | My First SQL book
  2 | Jane Smiley | My First SQL book
  3 | Alice Munro | My First SQL book
  1 | John Smith  | My Second SQL book
  2 | Jane Smiley | My Second SQL book
  3 | Alice Munro | My Second SQL book
  1 | John Smith  | My Third SQL book
```

This query is a little strange - rather than doing a traditional `INNER JOIN` it's returning the [Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) of our two tables which is essentially what a `CROSS JOIN` does. The result itself isn't very useful, but it does tells us something important:

**1) A JOIN adds data from the table referenced immediately after the `JOIN` command**

Even though this query was strange, one observation from it was that our `JOIN` added `books` table rows to our results data.

Thinking back to our original problem, our inability to access `users_table`, tells us something else.

**2) You can only utilize data already added to your results table**

This is a really important point for working with queries that join more than one table. In terms of writing queries on Many-to-Many relations, this lets us know that we need to we need to first access results that provides both a link user and books. We can retrieve data that links the `users` and `books` tables by first joining on a join table because that will return both user ids and book ids. In this case, `users_book` is the join table, so instead we need to first write a JOIN between our `users` to `user_books` tables.

 ```sql
/* Updating SELECT to show additional data */
 SELECT u.id, u.username, ub.user_id, ub.book_id
   FROM users u
  INNER JOIN users_books ub ON (ub.user_id = u.id);
 ```
The above query returns these results:

```
|id |  username   | user_id | book_id|
|----+-------------+---------+---------|
| 1 | John Smith  |       1 |       1|
| 1 | John Smith  |       1 |       2|
| 2 | Jane Smiley |       2 |       2|
```

Now it's time to match the books our user's checked out to users. The next JOIN continues to add data to our join table.

```sql
SELECT u.id, u.username, ub.user_id, ub.book_id, b.id AS book_table_id, b.title
  FROM users u
 INNER JOIN users_books ub ON (ub.user_id = u.id)
 INNER JOIN books b ON (b.id = ub.book_id);
```
```
id |  username   | user_id | book_id | book_table_id |       title   |     
----+-------------+---------+---------+---------+--------------------
| 1 | John Smith  |       1 |       1 |       1 | My First SQL book  |
| 1 | John Smith  |       1 |       2 |       2 | My Second SQL book |
| 2 | Jane Smiley |       2 |       2 |       2 | My Second SQL book |
```

This is the full query rather than the diff if that helps.

```sql
SELECT u.id, u.username, ub.user_id, ub.book_id, b.id AS book_id, b.title
  FROM users u
 INNER JOIN users_books ub ON (ub.user_id = u.id)
 INNER JOIN books b ON (b.id = ub.book_id);
```

Then all you need to do is be more selective with what data you show by changing your `SELECT` query to the below:

```sql
SELECT users.username, books.title
```

Which gets you this:

```
|  username   |       title        |
|-------------|--------------------|
| John Smith  | My First SQL Book  |
| John Smith  | My Second SQL Book |
| Jane Smiley | My Second SQL Book |
```

Hopefully the inner workings of JOINs on Many-to-Many relationships are a bit less opaque now.
