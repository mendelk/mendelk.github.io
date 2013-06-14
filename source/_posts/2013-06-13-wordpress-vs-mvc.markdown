---
layout: post
title: "WordPress vs. MVC"
date: 2013-06-13 17:23
comments: true
categories: 
---

### Or: What Pretty Models Say About You

In which I offer insight on the differences in data-model between the WordPress and MVC-like frameworks such as Rails, what led to them, and the implications for developing in them.
___

> Beauty and folly are old companions.
> - Benjamin Franklin

I've recently begun learning Ruby-on-Rails, and thus would like to discuss its data-modeling philosophy, specifically vis-a-vis that of WordPress, with which I am more familiar.

#### MVC

<div class="img-box left">

  {% img article-img /images/model-sign.jpg %}

  <br>

  <span class="caption">Pretty Model Held by Woman</span>

</div>

[MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) stands for Model-View-Controller.

If you've built a Rails app before, you are already familiar with what that means. In short: **Models** are an abstraction of your data (think database tables). **Controllers** respond to user input, and manipulate your models based on their allowed behaviors , and render data that is easily consumed by your **views**, seen by the user.

In MVC, we try very hard such that our Models, and hence our database, are "good" abstractions of our domain. What constitutes "good" MVC architecture is a bit subjective, and perhaps more art than science.

All would agree, however, that looking at a well-constructed MVC data-model should give you a very good idea about the nature and behavior of the application. 

It is in precisely this last point that WordPress most differs.

#### The WordPress Data Model

Out of the box, WordPress comes with [11 tables](http://codex.wordpress.org/Database_Description). I want to focus on two: the "posts" table, and the "postmeta" table.

"Posts" is a bit of a misnomer. In fact, the posts table is a general table for all types of objects. Where in MVC, you'd create a "products" model/table, and a "orders" model/table, in WordPress, objects of both types would go into the "posts" table. They'd be differentiated by the value in the "post_type" column, where one would presumably have the "products" value, while the other would have the value of "orders".

Parenthetically, Rails uses a similar convention on a much smaller scale to overcome the lack of support for is_a-like inheritance in database tables (see: Single Table Inheritance).

If you'll look closer at the "posts" schema, you'll discover that its properties are essentially what any object would require. Kinda. It has properties such as "date", "date_modified". It also has lots of others that would not always be desired.

The "postmeta" table is essentially key-value pairings for any custom property your object may have that isn't covered in the "posts" table. Its schema is (id), post_id, key, and value. So, say your product has a "price" property, that would go into the postsmeta table like so: post_id = [the product id], key = price, value = [the product price].

In SQL this might be expressed like this: `SELECT * FROM posts, postmeta WHERE posts.id = postmeta.post_id`, which would return a table with all the properties of that object. (In practice, such a query would break, because the same post_id can have multiple values associated with the same key.)

#### Why WordPress Chose Their Data Model
<div class="img-box right">

  {% img article-img http://imgs.xkcd.com/comics/physicists.png %}

  <br>

  <span class="caption"><a href="http://xkcd.com/793/">XKCD: Physicists</a></span>

</div>

If you look at the entire WordPress database schema, it becomes clear that, at least to begin with, WordPress designed their schema much like a Rails-dev would. Even the decision to keep posts and pages in one table can be justified in pure MVC.

But as WordPress moved farther away from being a tool tailored to blogging to being a general, multi-use CMS / E-commerce / Anything-you-want-it-to-be framework, the core team made the decision to keep 99% of the basic database structure. This is in line with the WordPress way of keeping updates, even major ones, extremely easy on the end-user, something major structural changes would preclude. That, in turn, has certainly been instrumental in making WordPress one of the most popular frameworks, powering some ~18% of the Internet. (Microsoft has adopted a similar strategy, with arguably [worse results](http://www.quora.com/Microsoft-Windows/Why-do-people-hate-Windows/answer/Balaji-Viswanathan-2?srid=Xjjv&share=1). Or [not](http://en.wikipedia.org/wiki/Usage_share_of_operating_systems).)

#### Implications
Many things in the world of programming boil down to personal taste. As a [great man](http://avi.flombaum.com) once said, "There's no wrong way to do it!" Personally, I disagree to an extent, but the point is clear.

Is developing with one data-model over another merely a matter of taste? I would say not. I would also posit that neither one is always right for the job, but they should be considered on a per-project basis.

My initial thoughts on each are so:

On the most simple of levels, the WordPress model is more flexible. Don't want to decide your objects' properties in advance? No problem. Want to format an entire form submission into json and save. Sure. Multiple values for one "column", and return as array. You bet. 

The price of that flexibility is paid in efficiency and hardiness. Clearly, queries which require a join run slower than those that don't (although the core team has done a remarkable job caching those queries, enabling WordPress installs that have millions of rows). Also, having constraints at the database level (this column value must be an integer, for example) will certainly be sturdier than the server-side constraints that have to be implemented for WordPress.

But I believe the biggest reason why someone would ultimately choose to go with Rails-like framework is not for the reasons mentioned above, but rather for reasons of beauty alone. A key-value schema is essentially formless. Good models must have form. Great models have great form. And the best are simply breathtakingly beautiful, and with that goes all reasons and reason.

