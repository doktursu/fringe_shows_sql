## SQL Questions

First create a database called fringe_shows
```
  #terminal
  psql
  create database fringe_shows;
  \q
```

Populate the data using the script - fringeshows.sql
```
  #terminal
  psql -d fringe_shows -f fringeshows.sql
```

Using the SQL Database file given to you as the source of data to answer the questions.  Copy the SQL command you have used to get the answer, and paste it below the question, along with the result they gave.


## Section 1

  Revision of concepts that we've learnt in SQL today

  1. Select the names of all users.

    fringe_shows=# select name from users;

           name       
    ------------------
     Rick Henri
     Jay Chetty
     Keith Douglas
     Callum Dougan
     Andrew Insley
     Daniel Gillespie
     Bethany Fraser
     Nick Ridell
     Evelyn Utterson
     Sky Su
     Nicholas Hill
     Michael McLeod
     Callum Hogg
     Chris Sloan
     Gary Carmichael
     Oscar Brooks
     Ross Galloway
     Paul MacLean
     Stuart Ramsay
     Peter Forbes
     Euan Walls
     Aine Dunphy
    (22 rows)

  2. Select the names of all shows that cost less than £15.

    fringe_shows=# select name from shows where price < 15.00;
                 name             
    ------------------------------
     Le Haggis
     Paul Dabek Mischief 
     Best of Burlesque
     Two become One
     Urinetown
     Two girls, one cup of comedy
    (6 rows)

  3. Insert a user with the name "Val Gibson" into the users table.

    fringe_shows=# insert into users (name) values ('Val Gibson');
    INSERT 0 1

  4. Select the id of the user with your name.

    fringe_shows=# select id from users where name = 'Sky Su';
     id 
    ----
     10
    (1 row)

  5. Insert a record that Val Gibson wants to attend the show "Two girls, one cup of comedy".

    fringe_shows=# insert into shows_users (show_id, user_id) values (12, 23); 
    INSERT 0 1

  6. Updates the name of the "Val Gibson" user to be "Valerie Gibson".

    fringe_shows=# update users set name = 'Valerie Gibson' where name = 'Val Gibson';
    UPDATE 1

  7. Deletes the user with the name 'Valerie Gibson'.

    fringe_shows=# delete from users where name = 'Valerie Gibson';
    DELETE 1

  8. Deletes the shows for the user you just deleted.

    delete from shows_users where user_id = 23;
    DELETE 1

    ---cleans rows of all users not existing---
    DELETE FROM shows_users WHERE user_id NOT IN (SELECT id FROM users)


## Section 2

  This section involves more complex queries.  You will need to go and find out about aggregate funcions in SQL to answer some of the next questions.

  9. Select the names and prices of all shows, ordered by price in ascending order.

    fringe_shows=# select name, price from shows order by price asc;
                      name                   | price 
    -----------------------------------------+-------
     Two girls, one cup of comedy            |  6.00
     Best of Burlesque                       |  7.99
     Two become One                          |  8.50
     Urinetown                               |  8.50
     Paul Dabek Mischief                     | 12.99
     Le Haggis                               | 12.99
     Joe Stilgoe: Songs on Film – The Sequel | 16.50
     Game of Thrones - The Musical           | 16.50
     Shitfaced Shakespeare                   | 16.50
     Aaabeduation – A Magic Show             | 17.99
     Camille O'Sullivan                      | 17.99
     Balletronics                            | 32.00
     Edinburgh Royal Tattoo                  | 32.99
    (13 rows)

  10. Select the average price of all shows.

    fringe_shows=# select avg(price) from shows;
             avg         
    ---------------------
     15.9569230769230769
    (1 row)

  11. Select the price of the least expensive show.

    fringe_shows=# select min(price) from shows;
     min  
    ------
     6.00
    (1 row)

  12. Select the sum of the price of all shows.

    fringe_shows=# select sum(price) from shows;
      sum   
    --------
     207.44
    (1 row)

  13. Select the sum of the price of all shows whose prices is less than £20.

    fringe_shows=# select sum(price) from shows where price < 20.00;
      sum   
    --------
     142.45
    (1 row)

  14. Select the name and price of the most expensive show.

    fringe_shows=# select name, price from shows where price = (select max(price) from shows);
              name          | price 
    ------------------------+-------
     Edinburgh Royal Tattoo | 32.99
    (1 row)

    fringe_shows=# select name, price from shows order by price desc limit 1;
              name          | price 
    ------------------------+-------
     Edinburgh Royal Tattoo | 32.99
    (1 row)

  15. Select the name and price of the second from cheapest show.

    fringe_shows=# select name, price from shows order by price asc offset 1 limit 1;
           name        | price 
    -------------------+-------
     Best of Burlesque |  7.99
    (1 row)

  16. Select the names of all users whose names start with the letter "N".

    fringe_shows=# select name from users where name like 'N%';
         name      
    ---------------
     Nick Ridell
     Nicholas Hill
    (2 rows)

  17. Select the names of users whose names contain "mi".

    fringe_shows=# select name from users where name like '%mi%';
          name       
    -----------------
     Gary Carmichael
    (1 row)

    ---ignore case---
    fringe_shows=# select name from users where upper(name) like upper('%mi%');
          name       
    -----------------
     Michael McLeod
     Gary Carmichael
    (2 rows)


## Section 3

  The following questions can be answered by using nested SQL statements but ideally you should learn about JOIN clauses to answer them.

  18. Select the time for the Edinburgh Royal Tattoo.

    fringe_shows=# select time from times join shows on times.show_id = shows.id where shows.name = 'Edinburgh Royal Tattoo';
     time  
    -------
     22:00
    (1 row)

    ---aliasing table names---
    fringe_shows=# select s.name, t.time from times as t join shows as s on t.show_id = s.id where s.name = 'Edinburgh Royal Tattoo';
     time  
    -------
     22:00
    (1 row)

  19. Select the number of users who want to see "Shitfaced Shakespeare".

    fringe_shows=# select count(user) from shows_users join shows on shows_users.show_id = shows.id where shows.name = 'Shitfaced Shakespeare';
     count 
    -------
         7
    (1 row)

  20. Select all of the user names and the count of shows they're going to see.

    fringe_shows=# select users.name, count(shows_users.show_id) as NumberOfShows from shows_users right join users on shows_users.user_id = users.id group by users.name; 

           name       | numberofshows 
    ------------------+---------------
     Gary Carmichael  |             4
     Michael McLeod   |             6
     Keith Douglas    |             6
     Stuart Ramsay    |             0
     Peter Forbes     |             0
     Callum Dougan    |             4
     Callum Hogg      |             4
     Sky Su           |             5
     Oscar Brooks     |             4
     Bethany Fraser   |             4
     Aine Dunphy      |             0
     Chris Sloan      |             4
     Daniel Gillespie |             4
     Ross Galloway    |             5
     Euan Walls       |             0
     Nicholas Hill    |             5
     Andrew Insley    |             4
     Nick Ridell      |             5
     Jay Chetty       |             5
     Rick Henri       |             5
     Evelyn Utterson  |             7
     Paul MacLean     |             0
    (22 rows)

    fringe_shows=# select users.name, count(shows_users.show_id) as NumberOfShows from users left join shows_users on users.id = shows_users.user_id group by users.name;

           name       | numberofshows 
    ------------------+---------------
     Gary Carmichael  |             4
     Michael McLeod   |             6
     Keith Douglas    |             6
     Stuart Ramsay    |             0
     Peter Forbes     |             0
     Callum Dougan    |             4
     Callum Hogg      |             4
     Sky Su           |             5
     Oscar Brooks     |             4
     Bethany Fraser   |             4
     Aine Dunphy      |             0
     Chris Sloan      |             4
     Daniel Gillespie |             4
     Ross Galloway    |             5
     Euan Walls       |             0
     Nicholas Hill    |             5
     Andrew Insley    |             4
     Nick Ridell      |             5
     Jay Chetty       |             5
     Rick Henri       |             5
     Evelyn Utterson  |             7
     Paul MacLean     |             0
    (22 rows)

    fringe_shows=# select users.name, count(shows_users.show_id) from shows_users right join users on shows_users.user_id = users.id group by users.id order by count asc;

           name       | count 
    ------------------+-------
     Euan Walls       |     0
     Paul MacLean     |     0
     Stuart Ramsay    |     0
     Peter Forbes     |     0
     Aine Dunphy      |     0
     Gary Carmichael  |     4
     Bethany Fraser   |     4
     Callum Dougan    |     4
     Chris Sloan      |     4
     Daniel Gillespie |     4
     Oscar Brooks     |     4
     Callum Hogg      |     4
     Andrew Insley    |     4
     Nick Ridell      |     5
     Rick Henri       |     5
     Nicholas Hill    |     5
     Ross Galloway    |     5
     Sky Su           |     5
     Jay Chetty       |     5
     Michael McLeod   |     6
     Keith Douglas    |     6
     Evelyn Utterson  |     7
    (22 rows)

  21. SELECT all users who are going to a show at 17:15.

    fringe_shows=# select * from users join shows_users on users.id = shows_users.user_id join times on shows_users.show_id = times.show_id where times.time = '17:15';
     id |      name       | id | show_id | user_id | id | time  | show_id 
    ----+-----------------+----+---------+---------+----+-------+---------
      1 | Rick Henri      | 17 |       3 |       1 |  3 | 17:15 |       3
      3 | Keith Douglas   | 18 |       3 |       3 |  3 | 17:15 |       3
      5 | Andrew Insley   | 19 |       3 |       5 |  3 | 17:15 |       3
      8 | Nick Ridell     | 20 |       3 |       8 |  3 | 17:15 |       3
      9 | Evelyn Utterson | 21 |       3 |       9 |  3 | 17:15 |       3
     13 | Callum Hogg     | 22 |       3 |      13 |  3 | 17:15 |       3
     15 | Gary Carmichael | 23 |       3 |      15 |  3 | 17:15 |       3
     11 | Nicholas Hill   | 33 |       6 |      11 |  6 | 17:15 |       6
     12 | Michael McLeod  | 34 |       6 |      12 |  6 | 17:15 |       6
     13 | Callum Hogg     | 35 |       6 |      13 |  6 | 17:15 |       6
     16 | Oscar Brooks    | 36 |       6 |      16 |  6 | 17:15 |       6
     17 | Ross Galloway   | 37 |       6 |      17 |  6 | 17:15 |       6
    (12 rows)

    fringe_shows=# select users.id, users.name from users join shows_users on users.id = shows_users.user_id join times on shows_users.show_id = times.show_id where times.time = '17:15';
     id |      name       
    ----+-----------------
      1 | Rick Henri
      3 | Keith Douglas
      5 | Andrew Insley
      8 | Nick Ridell
      9 | Evelyn Utterson
     13 | Callum Hogg
     15 | Gary Carmichael
     11 | Nicholas Hill
     12 | Michael McLeod
     13 | Callum Hogg
     16 | Oscar Brooks
     17 | Ross Galloway
    (12 rows)

    fringe_shows=# select users from users join shows_users on users.id = shows_users.user_id join times on shows_users.show_id = times.show_id where times.time = '17:15';
             users          
    ------------------------
     (1,"Rick Henri")
     (3,"Keith Douglas")
     (5,"Andrew Insley")
     (8,"Nick Ridell")
     (9,"Evelyn Utterson")
     (13,"Callum Hogg")
     (15,"Gary Carmichael")
     (11,"Nicholas Hill")
     (12,"Michael McLeod")
     (13,"Callum Hogg")
     (16,"Oscar Brooks")
     (17,"Ross Galloway")
    (12 rows)


## Hints

  - As with anything, if you get stuck, move on, then go back if you have time.
  - Don't spend all night on it!
  - Use resources online to solve harder ones - there are solutions to these questions that work with what we've learnt today, but other tools exist in SQL that could make the queries 'better' or 'easier'.
