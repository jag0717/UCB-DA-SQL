## SQL Assignment

### Problems

* 1a. Display the first and last names of all actors from the table `actor`. 

    	SELECT first_name, last_name FROM actor;

* 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`.

    	SELECT UPPER(CONCAT(first_name, ', ', last_name)) AS 'Actor Name' FROM actor;

* 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?

    	SELECT actor_id, first_name, last_name FROM actor WHERE first_name = 'Joe';

* 2b. Find all actors whose last name contain the letters `GEN`:

    	SELECT actor_id, first_name, last_name FROM actor WHERE last_name like '%GEN%';
  	
* 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order:

		SELECT actor_id, first_name, last_name FROM actor WHERE last_name like '%LI%'
		ORDER BY last_name, first_name;

* 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:

		SELECT country_id, country FROM country 
		WHERE country IN ('Afghanistan', 'Bangladesh', 'China');

* 3a. Add a `middle_name` column to the table `actor`. Position it between `first_name` and `last_name`. Hint: you will need to specify the data type.

		ALTER TABLE actor ADD COLUMN middle_name VARCHAR(45) NOT NULL AFTER first_name;
  	
* 3b. You realize that some of these actors have tremendously long last names. Change the data type of the `middle_name` column to `blobs`.

		ALTER TABLE actor CHANGE COLUMN middle_name middle_name BLOB NOT NULL;

* 3c. Now delete the `middle_name` column.

		ALTER TABLE actor DROP COLUMN middle_name;

* 4a. List the last names of actors, as well as how many actors have that last name.

		SELECT last_name, COUNT(last_name) FROM actor GROUP BY last_name;
  	
* 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

		SELECT last_name, COUNT(last_name) as ln_count FROM actor GROUP BY last_name HAVING ln_count >=2;
  	
* 4c. Oh, no! The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

		UPDATE actor SET first_name = 'HARPO' WHERE first_name = 'GROUCHO' AND last_name = 'WILLIAMS'; 	
  	
* 4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`. Otherwise, change the first name to `MUCHO GROUCHO`, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO `MUCHO GROUCHO`, HOWEVER! (Hint: update the record using a unique identifier.)

		UPDATE actor SET first_name = 'GROUCHO' WHERE first_name = 'HARPO' AND last_name = 'WILLIAMS'; 	

* 5a. You cannot locate the schema of the `address` table. Which query would you use to re-create it? HINT: [CHECK THIS OUT](https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html)

		SHOW CREATE TABLE actor;

* 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:

		SELECT s.first_name, s.last_name, a.address 
		FROM staff s
		JOIN address a
		ON (s.address_id = a.address_id);

* 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`. 

		SELECT s.staff_id, s.first_name, s.last_name, SUM(p.amount), P.payment_date 
		FROM payment p
		JOIN staff s
		USING (staff_id)
		WHERE payment_date between str_to_date('2005-08-01','%Y-%m-%d') AND str_to_date('2005-08-31','%Y-%m-%d')
		GROUP BY s.staff_id;

* 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.

		SELECT f.film_id, f.title, COUNT(fa.actor_id)
		FROM film f
		INNER JOIN film_actor fa
		ON (f.film_id = fa.film_id)
		GROUP BY fa.film_id;

* 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?

		SELECT f.film_id, f.title, COUNT(i.film_id)
		FROM inventory i
		JOIN film f
		USING (film_id)
		WHERE f.title = UPPER('Hunchback Impossible')
		GROUP BY i.film_id;

* 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:

		SELECT c.customer_id, c.first_name, c.last_name, sum(p.amount) as 'Total Amount Paid'
		FROM payment p
		JOIN customer c
		USING (customer_id)
		GROUP BY p.customer_id
		ORDER BY c.last_name;

* 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English.

		SELECT f.film_id, f.title
		FROM film f
		WHERE (title LIKE 'K%' OR title LIKE 'Q%')
		AND language_id IN
		(
		SELECT language_id
		FROM language
		WHERE name = 'English'
		);

* 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.

		SELECT a.actor_id, a.first_name, a.last_name
		FROM actor a
		WHERE a.actor_id IN 
		(
			SELECT fa.actor_id
			FROM film_actor fa
			WHERE fa.film_id IN
			(
				SELECT f.film_id
				FROM film f
				WHERE f.title = 'Alone Trip'
			)
		)ORDER BY a.first_name;

		* Alternate Solution

		SELECT f.film_id, f.title, a.first_name, a.last_name
		FROM film_actor fa
		JOIN film f ON (f.film_id = fa.film_id)
		JOIN actor a ON (fa.actor_id = a.actor_id)
		WHERE f.title = 'Alone Trip'
		ORDER BY a.first_name;
   
* 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

		SELECT cu.customer_id, cu.first_name, cu.last_name, cu.email, co.country  
		FROM customer cu
		JOIN address a USING(address_id)
		JOIN city c ON (a.city_id = c.city_id)
		JOIN country co ON (c.country_id = co.country_id)
		AND co.country = 'Canada';

* 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.

		SELECT f.title, c.name
		FROM film f
		JOIN film_category fc USING (film_id)
		JOIN category c USING (category_id)
		WHERE c.name = 'Family';

* 7e. Display the most frequently rented movies in descending order.

		SELECT f.film_id, f.title, COUNT(r.customer_id) AS rental_count
		FROM rental r
		JOIN inventory i USING (inventory_id) 
		JOIN film f USING (film_id)
		GROUP BY f.film_id
		ORDER BY rental_count DESC;

* 7f. Write a query to display how much business, in dollars, each store brought in.

		SELECT s.store_id, sum(p.amount) as 'Business(in $)'
		FROM store s
		JOIN payment p ON (p.staff_id = s.manager_staff_id)
		GROUP BY p.staff_id;

* 7g. Write a query to display for each store its store ID, city, and country.
  	
		SELECT s.store_id, c.city, co.country
		FROM store s
		JOIN address a USING(address_id)
		JOIN city c USING(city_id)
		JOIN country co USING(country_id);

* 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)

		SELECT ca.name AS Genre, SUM(p.amount) AS Revenue
		FROM category ca
		JOIN film_category fc USING(category_id)
		JOIN inventory i USING(film_id)
		JOIN rental r USING(inventory_id)
		JOIN payment p USING(rental_id)
		GROUP BY ca.category_id
		ORDER BY Revenue DESC LIMIT 5;

* 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.
  	
		CREATE VIEW top_five_genres AS 
		SELECT ca.name AS Genre, SUM(p.amount) AS Revenue
		FROM category ca
		JOIN film_category fc USING(category_id)
		JOIN inventory i USING(film_id)
		JOIN rental r USING(inventory_id)
		JOIN payment p USING(rental_id)
		GROUP BY ca.category_id
		ORDER BY Revenue DESC LIMIT 5;

* 8b. How would you display the view that you created in 8a?
		
		SHOW CREATE VIEW top_five_genres;
		SELECT * FROM top_five_genres;

* 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.

		DROP VIEW top_five_genres;
