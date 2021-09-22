# Sql-PL-SQL
Structured Query Language


--Hands On Assignments

--1:- Create a list that displays the title of each book and the name and phone number of the contact at the publisher's office for reordering each book.
--Ans:- 

SELECT title, contact, phone
FROM Books b, Publisher pb
WHERE b.pubid = pb.pubid;

SELECT title, contact, phone
FROM Books b JOIN Publisher pb
ON b.pubid = pb.pubid;

--Result:- 
TITLE                          CONTACT         PHONE      
------------------------------ --------------- ------------
BODYBUILD IN 10 MINUTES A DAY  RENEE SMITH     800-555-9743
REVENGE OF MICKEY              TOMMIE SEYMOUR  000-714-8321
BUILDING A CAR WITH TOOTHPICKS JANE TOMLIN     010-410-0010
DATABASE IMPLEMENTATION        DAVID DAVIDSON  800-555-1211
COOKING WITH MUSHROOMS         RENEE SMITH     800-555-9743
HOLY GRAIL OF ORACLE           DAVID DAVIDSON  800-555-1211
HANDCRANKED COMPUTERS          DAVID DAVIDSON  800-555-1211
E-BUSINESS THE EASY WAY        JANE TOMLIN     010-410-0010
PAINLESS CHILD-REARING         SEBASTIAN JONES 800-555-8284
THE WOK WAY TO COOK            RENEE SMITH     800-555-9743
BIG BEAR AND LITTLE DOVE       SEBASTIAN JONES 800-555-8284

TITLE                          CONTACT         PHONE      
------------------------------ --------------- ------------
HOW TO GET FASTER PIZZA        RENEE SMITH     800-555-9743
HOW TO MANAGE THE MANAGER      TOMMIE SEYMOUR  000-714-8321
SHORTEST POEMS                 SEBASTIAN JONES 800-555-8284

 14 rows selected


--2:- Determine which orders haven't yet shipped and the name of the customer who placed the order. Sort the results by the date on which the order was placed.
--Ans:- 

SELECT Order#, (firstname ||' '|| lastname) name
FROM orders o, customers c
WHERE o.customer# = c.customer#
AND o.shipdate IS NULL;

SELECT Order#, (firstname ||' '|| lastname) name
FROM orders o JOIN customers c
ON o.customer# = c.customer#
AND o.shipdate IS NULL;

--Result:- 

    ORDER# NAME                
---------- ---------------------
      1018 BONITA MORALES       
      1016 LEILA SMITH          
      1020 KENNETH JONES        
      1012 BECCA NELSON         
      1019 GREG MONTIASA        
      1015 KENNETH FALAH        

 6 rows selected 


--3:- Produce a list of all customers who live in the state of Florida and have ordered books about computers.
--Ans:- 

SELECT c.customer#, c.firstname ||' '|| c.lastname name, o.order#, oi.ISBN, b.category, c.state
FROM customers c, orders o, books b, orderitems oi
WHERE c.customer# = o.customer# 
AND o.order# = oi.order#
AND oi.isbn = b.isbn
AND STATE = 'FL'
AND b.category = 'COMPUTER';

SELECT customer#, c.firstname||' '||c.lastname name, order#, ISBN, b.category, c.state
FROM customers c JOIN orders o USING (customer#)
JOIN orderitems oi USING (order#)
JOIN books b USING (ISBN)
WHERE state = 'FL' AND category = 'COMPUTER';

--Result:- 
CUSTOMER# NAME                      ORDER# ISBN       CATEGORY     ST
---------- --------------------- ---------- ---------- ------------ --
      1001 BONITA MORALES              1018 8843172113 COMPUTER     FL
      1001 BONITA MORALES              1003 8843172113 COMPUTER     FL
      1003 LEILA SMITH                 1006 9959789321 COMPUTER     FL



--4:- Determine which books customer Jake Lucas has purchased. Perform the search using the customer name, not the customer number. If he has purchased multiple copies
      of the same books, unduplicate the results.
--Ans:- 

SELECT DISTINCT customers.firstname|| ' ' ||customers.lastname "NAME", books.title
FROM customers, orders, orderitems, books 
WHERE (customers.firstname|| ' ' ||customers.lastname) = 'JAKE LUCAS' 
AND customers.customer# = orders.customer# 
AND orderitems.order# = orders.order# 
AND books.ISBN = orderitems.ISBN;


SELECT DISTINCT customers.firstname|| ' ' ||customers.lastname "NAME", books.title
FROM customers INNER JOIN orders
ON customers.customer# = orders.customer#
INNER JOIN orderitems
ON orderitems.order# = orders.order# 
LEFT JOIN books
ON books.ISBN = orderitems.ISBN
WHERE (customers.firstname|| ' ' ||customers.lastname) = 'JAKE LUCAS';


--Result:- 
CUSTOMER# NAME                  ISBN       TITLE                        
---------- --------------------- ---------- ------------------------------
      1010 JAKE LUCAS            2491748320 PAINLESS CHILD-REARING        
      1010 JAKE LUCAS            9247381001 HOW TO MANAGE THE MANAGER   



--5:- Determine the profit of each book sold to Jake Lucas, using the actual price the customer paid (not the book's regular retail price). 
      Sort the results by order date. If more than one book was ordered, sort the results by profit amount in descending order. 
      Perform the search using the customer name, not the customer number.
--Ans:- 

SELECT orders.orderdate, books.title, (orderitems.paideach-books.cost) "PROFIT"
FROM customers, orders, orderitems, books 
WHERE customers.firstname = 'JAKE' 
AND customers.lastname = 'LUCAS' 
AND customers.customer# = orders.customer# 
AND orderitems.order# = orders.order# 
AND books.ISBN = orderitems.ISBN 
ORDER BY profit DESC, orderdate;

SELECT orders.orderdate, books.title, (orderitems.paideach-books.cost) "PROFIT"
From CUSTOMERS NATURAL JOIN orders 
NATURAL JOIN orderitems 
NATURAL JOIN books  
WHERE customers.firstname = 'JAKE' 
AND customers.lastname = 'LUCAS' 
ORDER BY profit DESC, orderdate;

--Result:- 
ORDERDATE TITLE                              PROFIT
--------- ------------------------------ ----------
31/MAR/09 PAINLESS CHILD-REARING              37.45
03/APR/09 PAINLESS CHILD-REARING              37.45
31/MAR/09 HOW TO MANAGE THE MANAGER           16.55


--6:- Which books were written by an author with the last name Adams? Perform the search using the uthor name.
--Ans:- 

SELECT books.title
FROM books, bookauthor, author
WHERE books.ISBN = bookauthor.ISBN 
AND bookauthor.authorid = author.authorid
AND author.lname = 'ADAMS';

SELECT books.title
FROM books NATURAL JOIN bookauthor
NATURAL JOIN author
WHERE author.lname = 'ADAMS';

--Result:- 
TITLE                          
------------------------------ 
DATABASE IMPLEMENTATION        


--7:- What gift will a customer who orders the book Shortest Poems receive? Use the actual book retail value to determine the gift.
--Ans:- 

SELECT gift
FROM books, promotion
WHERE books.title = 'SHORTEST POEMS';

SELECT gift
FROM books NATURAL JOIN promotion
WHERE books.title = 'SHORTEST POEMS';

--Result:- 
GIFT          
---------------
BOOKMARKER     
BOOK LABELS    
BOOK COVER     
FREE SHIPPING 


--8:- Identify the authors of the books Becca Nelson ordered. Perform the search using the customer name.
--Ans:- 

SELECT author.lname||','||author.fname "AUTHOR"
FROM customers, orders, books, orderitems, bookauthor, author
WHERE customers.customer# = orders.customer# 
AND orders.order# = orderitems.order#
AND orderitems.isbn = books.isbn
AND books.ISBN = bookauthor.ISBN
AND bookauthor.authorid = author.authorid
AND customers.lastname = 'NELSON'
AND customers.firstname = 'BECCA';

SELECT author.lname||','||author.fname "AUTHOR"
FROM customers NATURAL JOIN orders
NATURAL JOIN books
NATURAL JOIN orderitems
NATURAL JOIN bookauthor
NATURAL JOIN author
WHERE customers.lastname = 'NELSON'
AND customers.firstname = 'BECCA';


--Result:- 
AUTHOR              
---------------------
ROBINSON,ROBERT      
WHITE,WILLIAM        
WHITE,LISA           
BAKER,JACK           
ROBINSON,ROBERT      
FIELDS,OSCAR         
JONES,JANICE         

 7 rows selected 



--9:- Display a list of all books in the BOOKS table. If a book has been ordered by a customer, 
      also list the corresponding order number and the state in which the customer resides.
--Ans:- 

SELECT books.title, orders.order#, orders.shipstate
FROM books, orderitems, orders 
WHERE orderitems.order# = orders.order#(+)
AND books.isbn = orderitems.isbn(+);

SELECT books.title, orders.order#, orders.shipstate
FROM books LEFT JOIN orderitems 
ON books.isbn = orderitems.isbn 
LEFT JOIN orders 
ON orderitems.order# = orders.order#;

--Result:- 
TITLE                              ORDER# SH
------------------------------ ---------- --
COOKING WITH MUSHROOMS               1000 WA
PAINLESS CHILD-REARING               1001 GA
HOW TO MANAGE THE MANAGER            1001 GA
DATABASE IMPLEMENTATION              1002 IL
COOKING WITH MUSHROOMS               1003 FL
BODYBUILD IN 10 MINUTES A DAY        1003 FL
DATABASE IMPLEMENTATION              1003 FL
PAINLESS CHILD-REARING               1004 NJ
SHORTEST POEMS                       1005 GA
E-BUSINESS THE EASY WAY              1006 FL
DATABASE IMPLEMENTATION              1007 TX

TITLE                              ORDER# SH
------------------------------ ---------- --
BIG BEAR AND LITTLE DOVE             1007 TX
E-BUSINESS THE EASY WAY              1007 TX
HOLY GRAIL OF ORACLE                 1007 TX
COOKING WITH MUSHROOMS               1008 ID
REVENGE OF MICKEY                    1009 WA
COOKING WITH MUSHROOMS               1009 WA
DATABASE IMPLEMENTATION              1010 NJ
PAINLESS CHILD-REARING               1011 GA
REVENGE OF MICKEY                    1012 MI
PAINLESS CHILD-REARING               1012 MI
HANDCRANKED COMPUTERS                1012 MI

TITLE                              ORDER# SH
------------------------------ ---------- --
BIG BEAR AND LITTLE DOVE             1012 MI
DATABASE IMPLEMENTATION              1013 WY
REVENGE OF MICKEY                    1014 TX
COOKING WITH MUSHROOMS               1015 NJ
PAINLESS CHILD-REARING               1016 FL
BIG BEAR AND LITTLE DOVE             1017 FL
DATABASE IMPLEMENTATION              1018 FL
COOKING WITH MUSHROOMS               1018 FL
REVENGE OF MICKEY                    1019 GA
COOKING WITH MUSHROOMS               1020 WY
THE WOK WAY TO COOK                         

TITLE                              ORDER# SH
------------------------------ ---------- --
HOW TO GET FASTER PIZZA                     
BUILDING A CAR WITH TOOTHPICKS              

 35 rows selected


--10:- An EMPLOYEES table was added to the JustLee Books database to track employee information. Display a list of each employee's name, job title, and manager's name. 
       Use column aliases to clearly identify employee and manager name values. Include all employee in the list and sort by manager name.
--Ans:- 

SELECT e.empno, e.fname||' '||e.lname "EMPLOYEES", e.job, em.fname||' '||em.lname "MANAGER"
FROM employees e, employees em
WHERE e.empno(+) = em.mgr;

SELECT e.empno, e.fname||' '||e.lname "EMPLOYEES", e.job, em.fname||' '||em.lname "MANAGER"
FROM employees e JOIN employees em
ON e.empno = em.mgr;

--Result:- 
     EMPNO EMPLOYEES                            JOB
---------- ------------------------------------ ---------
MANAGER                            
------------------------------------
      7839 BEN KING                             GTECH2    
JIM POTTS                           

      7839 BEN KING                             GTECH2    
SAM SMITH                           

      7839 BEN KING                             GTECH2    
LARRY JONES                         


     EMPNO EMPLOYEES                            JOB
---------- ------------------------------------ ---------
MANAGER                            
------------------------------------
      8888 LARRY JONES                          MTech2    
SUE STUART                          

BEN KING                            


--ADVANCE CHALLENGE

SELECT b.title, pu.name "PUBNAME", b.retail, p.gift, b.retail-b.cost proft
FROM books b, publisher pu, promotion p
WHERE b.pubid =  pu.pubid
AND b.retail BETWEEN p.minretail AND p.maxretail
ORDER BY b.retail-b.cost;

TITLE                          PUBNAME                     RETAIL
------------------------------ ----------------------- ----------
GIFT                 PROFT
--------------- ----------
HANDCRANKED COMPUTERS          AMERICAN PUBLISHING             25 
BOOK LABELS            3.2

BIG BEAR AND LITTLE DOVE       REED-N-RITE                   8.95 
BOOKMARKER            3.63

COOKING WITH MUSHROOMS         READING MATERIALS INC.       19.95 
BOOK LABELS           7.45


TITLE                          PUBNAME                     RETAIL
------------------------------ ----------------------- ----------
GIFT                 PROFT
--------------- ----------
REVENGE OF MICKEY              PRINTING IS US                  22 
BOOK LABELS            7.8

THE WOK WAY TO COOK            READING MATERIALS INC.       28.75 
BOOK COVER            9.75

HOW TO GET FASTER PIZZA        READING MATERIALS INC.       29.95 
BOOK COVER            12.1


TITLE                          PUBNAME                     RETAIL
------------------------------ ----------------------- ----------
GIFT                 PROFT
--------------- ----------
BODYBUILD IN 10 MINUTES A DAY  READING MATERIALS INC.       30.95 
BOOK COVER            12.2

HOW TO MANAGE THE MANAGER      PRINTING IS US               31.95 
BOOK COVER           16.55

E-BUSINESS THE EASY WAY        PUBLISH OUR WAY               54.5 
BOOK COVER            16.6


TITLE                          PUBNAME                     RETAIL
------------------------------ ----------------------- ----------
GIFT                 PROFT
--------------- ----------
SHORTEST POEMS                 REED-N-RITE                  39.95 
BOOK COVER            18.1

BUILDING A CAR WITH TOOTHPICKS PUBLISH OUR WAY              59.95 
FREE SHIPPING        22.15

DATABASE IMPLEMENTATION        AMERICAN PUBLISHING          55.95 
BOOK COVER           24.55


TITLE                          PUBNAME                     RETAIL
------------------------------ ----------------------- ----------
GIFT                 PROFT
--------------- ----------
HOLY GRAIL OF ORACLE           AMERICAN PUBLISHING          75.95 
FREE SHIPPING         28.7

PAINLESS CHILD-REARING         REED-N-RITE                  89.95 
FREE SHIPPING        41.95


 14 rows selected 


SELECT b.isbn, b.title
FROM books b LEFT JOIN orderitems oi
ON b.isbn = oi.isbn
WHERE oi.isbn IS NULL;

ISBN       TITLE                        
---------- ------------------------------
4981341710 BUILDING A CAR WITH TOOTHPICKS
0132149871 HOW TO GET FASTER PIZZA       
0299282519 THE WOK WAY TO COOK           
