---
layout: post
title: "Learning SQL With Small Data Pt 1: Counting with JOINs and Subqueries"
comments: true
---

When I first started working with queries on multiple SQL tables, I was a bit lost. I would run a SQL query, but I wasn't always positive my results were correct. Particularly challenging were queries on entities that shared a Many-to-Many relationship. Now that I'm not so lost, I want to share something I figured out something along the way that helped me more easily pick up new SQL concepts - working with small data sets to test out queries.

### Starting small

As I was learning new SQL concepts, I found starting off with a small amount of data made it easier to:

1) Understand which data is being retrieved by your query

2) See how changing a query changes your results.

By the way, experimenting with a piece code or query is a [well-established way of learning](https://twitter.com/ThePracticalDev/status/720257210161311744/photo/1) in software development :)

For brevity's sake, in the future I’ll refer to the first benefit as "easy to understand", and the second as "easy to see how things change".

Below I go over some simple examples of retrieving data from multiple tables. The examples focus on finding counts on our results, since it's a common database tasks and reveals some of the intricacies of working with different types of table relationships. Before we dive in, let's first go over the data we'll be working with.

### The data

At the time of writing, I was going through a [SQL and databases course](https://launchschool.com/curriculum/courses/c48b1822) authored by a company called LaunchSchool, and ran into a small mock library database which I use in the examples below. If you have Postgres set up, start following along [here](https://launchschool.com/books/sql_first_edition/read/joins#adddata) for instructions on setting up the library database. If you're not sure how to create a Postgres database, start [here](https://launchschool.com/books/sql_first_edition/read/create_database) instead.

UPDATE: There's a [new version](https://launchschool.com/books/sql) of this book the examples reference. If the links above no longer work, you can recreate the database used in this post using this SQL file [here](https://gist.github.com/dylankb/4e0385efa247602418e6eb8c00775abb).

While the amount of data is small (there aren't that many rows or columns in the tables), I like this data set because we can still create interesting queries. As the book author describes it:

>Not all users have books.  
Not all users have an address.  
Not all users have reviews.  
Not all books are connected to users.  
Not all books have reviews.  

*Note: I briefly go over JOINs (`INNER JOIN` and `RIGHT / LEFT OUTER JOIN`), but I don't cover them from the ground up. If JOINs are new to you or you become confused, I'd highly recommend reading the LaunchSchool joins chapter linked to in the introduction or some other resource.*

In our investigation of joins we'll first look at using JOINs to accomplish a typical database task - counting things.

### Counting with JOINs

SQL gives you the ability to retrieve rows of data from your database through queries. For example, if you ask for all values in the `username` column from the `users` table, the output might look like this.

```sql
SELECT username
  FROM users;
```
```
 id |  username   
----+-------------
  1 | John Smith  
  2 | Jane Smiley
  3 | Alice Munro
```

In addition to retrieving data, databases can help us analyze "meta-information" about our results. One example are counts. Finding counts, or counting, means that our database is able to compute a numerical count on data returned by the query. For example, we can use SQL to count the number of rows that were returned.

```sql
SELECT COUNT(*)
  FROM users;
```
```
count
-------
    3
```

This is relatively simple task when we're dealing with one table, but things get a little more complicated when we go to perform queries on relationships that span multiple tables. Our first example of counting things with SQL is counting entities that have a One-to-One relationship with another entity.

#### One-to-One relationships

The two tables in this example are `users` and `addresses`. A user can have only one address and an address can have only one user. A user (or user instance) represents one row in the users table, and likewise for an address with the addresses table. This limitation on the relationship is why users and addresses have a "One-to-One" relationship.

If you look up the [definition](https://en.wikipedia.org/wiki/One-to-one_(data_model)) of a One-to-One relationship, you'll see that all the One-to.. and Many-to.. type relationships are a way to describing how many entity instances are there on each side of the relationship. The word used to describe this aspect of the relationship is called **cardinality**.

The basics of counting with One-to-One relationships are pretty easy, especially when using `INNER JOIN`. Let's look at an example. First, we'll look for all users who have an address.

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

An `INNER JOIN` "...returns a result set that contains the common elements of the tables..."[^1], so we can describe the query above as counting the number of users (by `id`) who have an `id` that matches a `user_id` column in an `addresses` table row. It's a simple query and we understand the cardinality (relationship type) we're joining on, so it's probably correct. The nice thing about working with small data sets is that we don't have to settle for "probably correct" - we can check it out for ourselves!

Let's change our query to not restrict results in the `SELECT` statement, but instead show more results returned by the JOIN. Note that I'm aliasing the `users` table with a `u` and the `addresses` table with an `a`.

```sql
SELECT u.id, u.username, a.street, a.city, a.state
  FROM users u
 INNER JOIN addresses a ON (a.user_id = u.id);
```
```
 id |  username   |     street      |     city      | state
----+-------------+-----------------+---------------+-------
  1 | John Smith  | 1 Market Street | San Francisco | CA
  2 | Jane Smiley | 2 Elm Street    | San Francisco | CA
```

We can easily see that there are two users in our results, so the count is correct. By the way, this was an example of advantage #1 to using small data sets - "easy to understand".

If we recall the description of this data set from earlier, the user-address relationship was described as "Not all users have an address." If we wanted to triple check that we've only found users with an address, we can change our `INNER JOIN` to a `LEFT OUTER JOIN`. A `LEFT OUTER JOIN` returns all rows from the left table, in this case the `users` table, even if there are no matching rows to the table it is JOINing.

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

We can see that Alice Munro does not have an address, and so she was correctly excluded from our results set in the previous query counting users with an address.

We made a small change to our query by which resulted in a small change to our output. I'd say that counts as an example of advantage #2 - "easy to see how things change".

#### Counting on a One-to-Many relationship

The next relationship type to explore is One-to-Many. A book has many reviews and a review can only pertain to one book, so it's an example of a One-to-Many. Let's try finding the count of books with reviews.

```sql
SELECT COUNT(books.title)
  FROM reviews
 INNER JOIN books ON (books.id = reviews.book_id);
```
```
3
```

This result appears to indicate that there are three books with reviews. To double check the accuracy of our result, let's reveal more columns from the data we're returning by changing our `SELECT` statement to `SELECT reviews.id, reviews.review_content, books.title` and run the query again.

```
 id |  review_content  |       title        
----+------------------+--------------------
  1 | My first review  | My First SQL book
  3 | review_content   | My Second SQL book
  2 | My second review | My Second SQL book
```

As we can see two of the books are duplicates, so there are actually only only two unique books. Why is that, and how can we fix our query above to return the correct count?

JOINs link two tables based on one or more fields by returning each row where a match is present between the field(s). To be more specific about the problem we're encountering, if there are multiple reviews of a book, then a simple count on `books.title` will return that same book title as many times as there are reviews for that book.

To fix the duplicates problem, we can include the `DISTINCT` keyword to limit our results set to unique books.

```sql
SELECT COUNT(DISTINCT books.title)
  FROM reviews
 INNER JOIN books ON (books.id = reviews.book_id);
```
```
2
```

#### Counting with Subqueries

Let's look at subqueries because they work a bit differently. We'll see how so when we go to perform the same count as in our previous JOIN example.

```rb
SELECT COUNT(books.id)
  FROM books
 WHERE id IN (SELECT book_id FROM reviews);
```
```
2
```

We already know that `2` is the correct answer, but let's break things down further to understand why we got the correct answer. The inner subquery from above, `SELECT book_id FROM reviews`, returns:

```
 book_id
---------
       1
       2
       2
```

Because we're returning three results, it would be normal to expect that we might get a count of `3` back. However one of the fundamental differences with subqueries is that the logic is performed in the `WHERE` clause. In this case, `WHERE`...`IN` is doing the heavy lifting for us by checking for books ids against those in the `books` table.

To narrate what's happening in plain English, the above query is essentially saying, "For each book id in the books table, check if the id is included in the list of book ids from the `reviews` table (the ones returned by the inner subquery)". In this example, subqueries ensures that we don't need to worry about duplicate ids in our results.

We touched on a lot of SQL subjects above: JOINS, subqueries, table relationships, and counting to name a few. If you found any of the above useful to you and would like to continue exploring SQL, feel free to read my [next post]({{ site.url }}{% link _posts/2017-11-5-learning-sql-with-small-data-pt-2-breaking-apart-a-many-to-many-relationship.md %}) in the series on breaking down how to work with a Many-to-Many table relationships.

[^1]: https://launchschool.com/books/sql/read/joins#innerjoins
