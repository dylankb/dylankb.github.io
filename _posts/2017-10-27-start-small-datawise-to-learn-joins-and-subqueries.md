---
layout: post
comments: true
title: Start Small (Datawise) To Learn SQL JOINs and Subqueries
---

When I first started working with queries on multiple SQL tables I was a bit lost. Particularly challenging were queries on entities that shared a Many-to-Many relationship. Along the way I figured out something that helped me more easily pick up new SQL concepts that I wanted to share - working with small sets of data to test out queries.

### Starting small

I found that when I was learning new SQL concepts, starting off with a small amount of data made it easier to:

1) Understand what data is being retrieved by your query

2) See how changing a query changes your results.

I might add that this last one is a [well-established way of learning](https://twitter.com/ThePracticalDev/status/720257210161311744/photo/1) in software development :)

For brevity's sake, when I refer to the two benefits listed above I'll refer to 1) "easy to understand the data source", and 2 as "easy to see how things change" from now on.

Below I use these two advantages of small data to break down the various querying methods for different types of table relationships. In particular, I focus on exploring SQL counting operations and queries on Many-to-Many relationships. First, let's go over the data we'll working with.

### The data

At the time of writing, I was going through a [SQL and databases course](https://launchschool.com/curriculum/courses/c48b1822) authored a company called LaunchSchool, and ran into a small but great fake library database you can set up using instructions in their free ebook that covers SQL joins [here](https://launchschool.com/books/sql/read/joins#adddata).

I like this data set because while the amount of data is small (there aren't that many rows or columns in the tables), we can still make interesting queries. As the book author describes it:

>Not all users have books.  
Not all users have an address.  
Not all users have reviews.  
Not all books are connected to users.  
Not all books have reviews.  

*We'll be using joins (`INNER JOIN` and `RIGHT / LEFT OUTER JOIN`) so if these concepts are new to you, I'd highly recommend reading the LaunchSchool book up to the joins chapter linked to in the introduction. If you want to follow along then set up the library database from the book, too!*

In our investigation of joins we'll first look at using JOINs to accomplish a typical database task - counting things.

### Counting with JOINs

SQL obviously gives you the ability query for rows of data from your database, but it also provides ways of analyzing "meta information" about your queried results. One example common SQL analysis task is counting. By counting, we mean that our database is able to compute a numerical count about some type of data returned for a given query. For example, we can use SQL to count the number of rows that were returned for a given query.

Our first example of counting things with SQL will be an example of counting on a One-to-One relationship.

#### One-to-One relationship

If you look up the [technical definition](https://en.wikipedia.org/wiki/One-to-one_(data_model)) of a One-to-One relationship, you'll see that all One-to.., Many-to.. type relationships are a way of describing the **cardinality**, or number of instances on each side of the relationship. Our two entities in this example are a user and an address, and since it's a One-to-One relationship a user belongs to one address and an address belongs to one user.

The basics of counting with One-to-One relationships are pretty easy, especially when using `INNER JOIN`. Let's look at an example. First we'll look for all users who have an address.

```sql
SELECT COUNT(users.id)
  FROM users
 INNER JOIN addresses ON (addresses.user_id = users.id);
```
```
 count
-------
     2
```

An `INNER JOIN` "...returns a result set that contains the common elements of the tables..."[^1], so we can describe the query above as counting the number of users (by `id`) who have an `id` that matches a `user_id` column in an `addresses` table row. It's a pretty simple query and we understand the cardinality (relationship type) we're joining on, so it's probably correct. The nice thing about working with small data sets is that we don't have to settle for "probably correct" - we can check it out for ourselves!

Let's change our query to not restrict results in the `SELECT` statement, but instead show more results returned by the JOIN. Note that I'm aliasing the `users` table with a `u` and the addresses table with an `a`.

```sql
SELECT u.id, u.username, a.street, a.city, a.state
  FROM users u
 INNER JOIN addresses a ON (a.user_id = u.id);
```
```
 id |  username   |     street      |     city      | state
 id |  username   |     street      |     city      | state
----+-------------+-----------------+---------------+-------
  1 | John Smith  | 1 Market Street | San Francisco | CA
  2 | Jane Smiley | 2 Elm Street    | San Francisco | CA
```

We can easily see that two users in our results, so the count is correct. By the way, this was an example of advantage 1) of using a small data set - "easy to understand the data source".

If we recall the description of this dataset from earlier, the user-address relationship was described as "Not all users have an address." If we wanted to triple-check that we've only found users with an address, we can change our `INNER JOIN` to a `LEFT OUTER JOIN`.

```sql
SELECT u.id, u.username, a.street, a.city, a.state
  FROM users u
  LEFT OUTER JOIN addresses a ON (a.user_id = u.id);
```
```
 id |  username   |     street      |     city      | state
----+-------------+-----------------+---------------+-------
  1 | John Smith  | 1 Market Street | San Francisco | CA
  2 | Jane Smiley | 2 Elm Street    | San Francisco | CA
  3 | Alice Munro |                 |               |             |
```

We can see that Alice Munro does not have an address, so this is indeed the correct result.

We made a small change to our query which resulted in a small change to our output. I'd say that counts as an example of advantage 2) "easy to see how things change".

#### Counting on a One-to-Many relationship

A book has many reviews, so let's try finding the count of books with reviews.

```sql
SELECT COUNT(books.title)
  FROM reviews
 INNER JOIN books ON (books.id = reviews.book_id);
```
```
3
```

To double check the accuracy of our result, let's reveal a bit more of the data we're returning by changing our `SELECT` statement to `SELECT reviews.id, reviews.review_content, books.title` and run the query again.

```
 id |  review_content  |       title        
----+------------------+--------------------
  1 | My first review  | My First SQL book
  3 | review_content   | My Second SQL book
  2 | My second review | My Second SQL book
```

So, we're actually only getting getting three rows back, but only two unique books

JOINs link two tables based on one or more fields by returning each row where a match is present between the field(s). To be more specific about the problem we're encountering, if there are multiple reviews of a book then a simple count on `books.title` will return that same book title `n` number of times where `n` is the number of reviews for that book.

As we can see, we need to be a bit more careful when executing JOINs on non One-to-One relationships. To fix this problem, we'll include the `DISTINCT` keyword to limit our results set to unique books.

```sql
SELECT COUNT(DISTINCT books.title)
  FROM reviews
 INNER JOIN books ON (books.id = reviews.book_id);
```
```
2
```

#### Counting with Subqueries

Let's look at subqueries, because subqueries work a bit differently, and we'll see that when we go to perform the same count as in our previous JOIN example.

```rb
SELECT COUNT(books.id)
  FROM books
 WHERE id IN (SELECT book_id FROM reviews);
```
```
2
```

We already know that `2` is the correct answer, but we'll break things down a bit to understand why we got the correct answer.

The subquery `SELECT book_id FROM reviews` returns:

```
 book_id
---------
       1
       2
       2
```

Because we're returning three results, it would be normal to expect that we might get a count of `3` back. However one of the fundamental differences in subqueries is that the logic is performed in the `WHERE` clause and not a joining of the results.

To be a bit more clear, the above query is essentially saying "For each book, check if it's id is included in other set (or subquery) of ids". In this example, subqueries make counting a bit easier. :)

### Working with Many-to-Many Relationships

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

When you break something and don't know why, that's a clear indication that you have some gaps in your knowledge. What helped me finally get a strong grasp of how this type of join worked was to use some of the tools we've been practicing: displaying the data available to us, checking how this data can change, and using a small dataset to do so.

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

Hopefully the inner workings on JOINs on Many-to-Many relationships are a bit less opaque now.

[^1]: https://launchschool.com/books/sql/read/joins#innerjoins
