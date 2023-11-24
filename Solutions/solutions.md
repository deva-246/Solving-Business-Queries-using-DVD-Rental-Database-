## Solutions

Which actors have the first name ‘Scarlett’?

          select * from actor where first_name like 'Scarlett'
 

Which actors have the last name ‘Johansson’? 

           select * from actor where last_name like 'Johansson'
 

How many distinct actors' last names are there? 

             select count(distinct(last_name)) from actor
    

Which last names are not repeated? 

         select distinct(last_name) from actor
    

Which last names appear more than once? 

             select last_name, count(*) 
             
             from actor
             
             group by last_name
             
             having count(*) > 1;
           

Which actor has appeared in the most films? 

            select a.actor_id,concat(a.first_name,' ',a.last_name) as ActorName,count(b.film_id) as countofmovies from actor a
            
            join film_actor b on a.actor_id = b.actor_id
            
            join film c on c.film_id = b.film_id  GROUP BY a.actor_id
            
            HAVING COUNT(*) > 1  order by countofmovies DESC

 


Is ‘Academy Dinosaur’ available for rent from Store 1? 

          select count(b.film_id) as Copiesofstore1 from
          
          store a 
          
          join inventory b on a.store_id = b.store_id
          
          join  film c on c.film_id = b.film_id
          
          where c.title = 'Academy Dinosaur' and b.store_id = 1
           


Insert a record to represent Mary Smith renting ‘Academy Dinosaur’ from Mike Hillyer at Store 1 today . 
select film_id from film where title = 'Academy Dinosaur'
select staff_id,first_name,last_name from staff where first_name = 'Mike' and last_name='Hillyer' and store_id=1;
select customer_id,first_name,last_name from customer where first_name = 'Mary' and last_name='Smith';
insert into rental(rental_date,inventory_id,customer_id,staff_id) values(now(),1,1,1)

select * from rental where inventory_id =1 and customer_id=1 and staff_id=1
 


When is ‘Academy Dinosaur’ due?
select title,concat(rental_duration,' ','days from the rental date') 
from film 
where film_id in (select film_id from inventory where film_id=1);
 

What is the average running time of all the films in the sakila DB? 
select avg(length) from film;
 

What is the average running time of films by category? 
select c.name,avg(a.length)
from film a
join 
film_category b on b.film_id = a.film_id
join 
category c on b.category_id = c.category_id
group by c.name
 


Set 2: -
 Write a query to find the id, first name, and last name of an actor whose first name is like 'JOE'.
select  actor_id,first_name,last_name from actor
where  first_name like 'JOE' – No results found
(or)
select  actor_id,first_name,last_name from actor
where  first_name like 'JOE' or first_name like 'Joe';
 
 Find all the actors, whose last name contains the letters 'GEN'. Make this case insensitive. 
select actor_id,first_name,last_name from actor
where last_name like 'GEN' or last_name like 'gen';    - No results from actor table

Add a middle_name column to the table actor. Specify the appropriate column type 
alter table actor 
ADD Column middle_name varchar(60); 
select * from actor
 

Now write a query that would remove the middle_name column
alter table actor
DROP Column middle_name
select * from actor
 

 List the last names of actors, as well as how many actors have that last name. 
select last_name,count(last_name) 
from actor 
where last_name is not null
group by last_name
 

List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors. 
select last_name,count(last_name)  from actor  where last_name is not null
group by last_name  having count(last_name)>=2
 

The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS. Write a query to fix the record 
update actor
set first_name = 'HARPO'
where first_name = 'GROUCHO' and last_name = 'WILLIAMS'

Perhaps we were too hasty in changing GROUCHO to HARPO. It turns out that GROUCHO was the correct name after all! In a single query, if the first name of the actor is currently HARPO, change it to GROUCHO. Otherwise, change the first name to MUCHO GROUCHO, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO MUCHO GROUCHO(Hint: update the record using a unique identifier.)
update actor
 set first_name = 
 case 
 when first_name = 'Harpo' 
 then 'Groucho'
 else 'MUCHO GROUCHO'
 end
 where actor_id = 172;


Use a JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address: 
select a.first_name,a.last_name,b.address
from staff a 
join address b on a.address_id = b.address_id
 


Use a JOIN to display the total amount rung up by each staff member in January of 2007. Use tables, staff and payment.
select concat(s.first_name,' ',s.last_name), sum(p.amount) AS "Totally Paid"
from  staff  s  join  payment  p  on p.staff_id = s.staff_id
where  EXTRACT(month FROM p.payment_date) = 1 AND  
EXTRACT(year FROM p.payment_date) = 2007
group by p.staff_id,s.first_name,s.last_name;

List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.
select a.title filmname,count(b.actor_id) numberofactors 
from 
film a join film_actor b on a.film_id= b.film_id
group by a.title
 


 How many copies of the film Hunchback Impossible exist in the inventory system? 
select b.title,count(a.film_id) AS "count of copies"
from inventory a 
join
film b on a.film_id = b.film_id
where b.title = 'Hunchback Impossible'
group by b.title;
 

Using the tables payment and customer and the JOIN command, list the total paid by each customer 
select b.customer_id,concat(b.first_name,' ',b.last_name) AS "Customer Name",sum(a.amount) as "Total amount paid"
from payment a 
join customer b on a.customer_id = b.customer_id
group by b.customer_id 
order by b.customer_id
 

The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters K and Q have also soared in popularity. display the titles of movies starting with the letters K and Q whose language is English 
select a.title AS "Film Names with K and Q" from film a 
join language b on a.language_id = b.language_id
where a.title LIKE 'Q%' or a.title like 'K%' and b.name = 'English';
 


Use subqueries to display all actors who appear in the film Alone Trip
select first_name, last_name
from actor 
where  actor_id  in 
( select  actor_id  from film_actor
  where film_id = ( 
select film_id from film where title = 'alone trip));
 
