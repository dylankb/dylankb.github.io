---
layout: post
title: "Learning SQL With Small Data Pt 1: Counting with JOINs and Subqueries"
comments: true
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

[^1]: https://launchschool.com/books/sql/read/joins#innerjoins
