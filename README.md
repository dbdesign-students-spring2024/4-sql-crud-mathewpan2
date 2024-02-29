# SQL CRUD


## Restaurant Finder

### Creating Table

```
create table restaurants (
   id INTEGER PRIMARY KEY,
   name TEXT,
   category TEXT,
   price TEXT,
   neighborhood TEXT,
   opening_hours TEXT,
   closing_hours TEXT,
   average_rating INTEGER,
   good_for_kids INTEGER,
   created DATETIME DEFAULT CURRENT_TIMESTAMP
)

create table reviews (
    id INTEGER PRIMARY KEY,
    review TEXT,
    restaurant_id INTEGER NOT NULL,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants (id),

)

```

[restaurants](data/restaurants.csv)

[reviews](data/reviews.csv)

### Importing csv data

```
$ .mode csv
$ .import data/resturant.csv resturants 
$ .import data/reviews.csv reviews
```

### Queries

1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```
select * from restaurants where neightborhood='brooklyn' and price='cheap';
```
2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.

```
select * from restaurants where category='pizza' and average_rating >= 3 order by average_rating;
```

3. Find all restaurants that are open now (see hint below).

```
select * from restaurants where strftime('%H:%M', 'now', 'localtime') between 
opening_hours and closing_hours;
```
4. Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).

```
insert into reviews (review, resturant_id) values
    ('I didn't like the food', (select id from restaurants where name='pizza hut'));
```

5. Delete all restaurants that are not good for kids.

```
delete from restaurants where good_for_kids = 0
```

6. Find the number of restaurants in each NYC neighborhood.

```
select neighborhood, count(*) from restaurants group by neighborhood;

```

## Social media app 

### Creating table
```
create table users(
    id INTEGER PRIMARY KEY,
    email TEXT,
    password TEXT,
    handle TEXT,
)

create table posts(
    id INTEGER PRIMARY KEY,
    type TEXT NOT NULL,
    FOREIGN KEY (author_id) REFERENCES users (id) NOT NULL,
    FOREIGN KEY (receiver_id) REFERENCES users (id),
    content TEXT,
    visible INTEGER DEFAULT 1,
    created DATETIME DEFAULT CURRENT_TIMESTAMP,
)
```
[users](data/users.csv)

[posts](data/posts.csv)


### Importing csv data
```
$ .mode csv
$ .import data/users.csv users 
$ .import data/posts.csv posts
```
### Queries


1. Register a new User.

```
insert into users (email, password, handle)
    values ('mail@email.com', '123456', 'mailguy');
```

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```
insert into posts (type, author_id, receiver_id, content )
    values ('message', (select id from users where email='mail@email.com'), (select id from users where email='guy@email.com'), 'I don't like you');
```

3. Create a new Story by a particular User (pick any User for example).

```
insert into posts (type, author_id, content)
    values ('story', (select id from users where email='mail@email.com'), 'Just got married.')
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.
```
select * from posts where visible = 1 order by created limit 10;
```

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.

```
select * from posts where visible = 1 and author_id = 1 and receiver_id = 2 order by created limit 10;
```

6. Make all Stories that are more than 24 hours old invisible.
```
update posts 
set visible = 0
where type = 'story' and ROUND((JULIANDAY('now') - JULIANDAY(created)) * 24) > 24;
```

7. Show all invisible Messages and Stories, in order of recency.

```
select * from posts where visible = 0 order by created;
```

8. Show the number of posts by each User.

```
select users.handle, count(*)
from users
join posts on users.id = posts.author_id
group by users.id

```

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.

```
select posts.content, users.email 
from users
join posts on users.id = posts.author_id
where ROUND((JULIANDAY('now') - JULIANDAY(posts.created)) * 24) > 24;
```

10. Show the email addresses of all Users who have not posted anything yet.

```
select users.email 
from users
left join posts on users.id = posts.author_id
where posts.author_id is NULL;
```










