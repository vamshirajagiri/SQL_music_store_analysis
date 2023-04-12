# SQL_music_store_analysis
Through a comprehensive analysis of the music store database, I was able to provide actionable insights to several important business inquiries. The resulting findings demonstrate a deep understanding of the database structure and effectively utilize SQL querying.

# Easy Questions:
/* Q1: Who is the senior most employee based on job title? */

> select * from employee 
order by levels desc
limit 1;

/* Q2: Which countries have the most Invoices? */

> select billing_country,count(*) as no_of_invoices  from invoice
group by billing_country
order by no_of_invoices desc; 

/* Q3: What are top 3 values of total invoice? */

> select total from invoice
order by total desc
limit 3;


/* Q4: Which city has the best customers?
We would like to throw a promotional Music Festival in the city we made the most money. 
Write a query that returns one city that has the highest sum of invoice totals. 
Return both the city name & sum of all invoice totals */

> select billing_city ,
sum(total) as sum_of_invoice 
from invoice
group by billing_city
order by sum_of_invoice desc;



/* Q5: Who is the best customer? 
The customer who has spent the most money will be declared the best customer. 
Write a query that returns the person who has spent the most money.*/


> select customer.customer_id,
customer.first_name,customer.last_name,sum(invoice.total) as amnt_spent
from customer
join invoice on  customer.customer_id = invoice.customer_id
group by customer.customer_id
order by amnt_spent desc
limit 1;

# Moderate Questions
/* Q1: Let's invite the artists who have written the most rock music in our dataset. 
Write a query that returns the Artist name and total track count of the top 10 rock bands. */

 > select  artist.artist_id as id,artist.name as Artist,count(*) as track_count from artist
 join album on artist.artist_id=album.artist_id
 join track on track.album_id = album.album_id
 join genre on genre.genre_id=track.genre_id
 where genre.name like 'Rock'
 group by Artist,id
 order by track_count desc
 limit 10;



/* Q2: Return all the track names that have a song length longer than the average song length. 
Return the Name and Milliseconds for each track. 
Order by the song length with the longest songs listed first. */

> select track_id, name,milliseconds from track
where milliseconds> (select avg(milliseconds) from track)
order by milliseconds desc;

/* Q3: Write query to return the email, first name, last name, & Genre of all Rock Music listeners. 
Return your list ordered alphabetically by email starting with A. */

> select distinct customer.email as Email,
customer.first_name as Fname, customer.last_name as Lname,genre.name from customer
join invoice on customer.customer_id = invoice.customer_id
join invoice_line on invoice.invoice_id = invoice_line.invoice_id
join track on track.track_id= invoice_line.track_id
join genre on genre.genre_id= track.genre_id
where genre.name like 'Rock'
order by email;



# Hard Questions

/* Q1: We want to find out the most popular music Genre for each country. 
We determine the most popular genre as the genre 
with the highest amount of purchases. Write a query that returns each country 
along with the top Genre. For countries where 
the maximum number of purchases is shared return all Genres. */



> with popular_genre as (

select count(il.quantity)  as purchases,c.country as country,g.genre_id as id,g.name as name,
row_number() over(partition by c.country order by count(il.quantity) desc) as row
from invoice_line il 
join invoice i on il.invoice_id=i.invoice_id
join customer c on i.customer_id=c.customer_id
join track on track.track_id=il.track_id
join genre g on g.genre_id=track.genre_id
group by 2,3,4

)
> select * from popular_genre
where row <=1;


/* Q2: Write a query that determines the customer that has spent the most on music for each country. 
Write a query that returns the country along with the top customer and how much they spent. 
For countries where the top amount spent is shared, provide all customers who spent this amount. */

> with a as( 

select customer.customer_id as c,first_name,last_name,billing_country,sum(total),
row_number() over(partition by country order by sum(total) desc) as rowss from customer
join invoice on customer.customer_id=invoice.customer_id
group by 1,2,3,4
	order by 4 asc
)

> select * from a where rowss<=1;




















