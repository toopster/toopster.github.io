---
layout: post
title:      "Time poor not poor time management"
date:       2021-09-07 09:00:00 -0400
permalink:  time_poor_not_poor_time_management
---

One thing that I have learnt from from my professional career to-date is that time is never on your side and that technical projects in the real world are rarely delivered as perfectly as you plan (or hope) them to be.  In this blog post I share some tips, technical and not-so technical, obvious and not-so obvious, from my working in industry.

As I did my usual staring at a blank screen before starting this blog post, I reflected on some of the different projects that I have worked on throughout my career and how I might have done them differently had I known at the start of the project what I knew at the end.  Indeed one of the digital agencies that I used to work with would tell me that, you can have the best requirements gathering processes and functional specifications, you never really discover the full project brief until it is delivered.

More than anything, the commodity of time has always been in short supply. 

From the project where my team and I delivered an enterprise level entry, logging and judging platform for an Awards company in a little over 3 months when, in a perfect world, it should have taken 9 months to complete – to my weekly freelance work for a Student Accommodation company, their business decimated by the global pandemic, where I try to squeeze in as much work as possible in the 3 hours a week I work for them.

## Google is your friend (and faux)

<img src="post_images/2021-09-07-google_quote.jpg" alt="Liane Moriarty Google Quote" />

It's an incredibly obvious tool but I have found myself using Google to resolve a problem almost daily.  

I might be knee deep in code, attempting to execute a task and I either can't remember how to do something or it's something completely brand new to me.  

Either way, rather than eat up valuable time trying to figure it out myself, the result of a Google search will quickly get me on a path towards a solution.

Unfortunately, whilst there is a huge amount of helpful technical content out there on the internet, there is almost an equal amount of unhelpful or incorrect technical content.  After a while though, you begin to build up a sense of those sources and websites that can be trusted and those that can't, or at least should be viewed with a sense of caution.

<img src="post_images/2021-09-07-keep_calm_and_google_it" alt="Keep Calm and Google It" />

## Don't be afraid to ask for help

## Simple solutions are fast and cost effective

![Gaffer Tape Solutions](post_images/2021-09-07-gaffer_tape_solutions.jpg)

I affectionately refer to these as "gaffer tape solutions" (duct tape if you're in the US), they get the job done allbeit in a slightly ugly fashion, but they won't necessarily expand your coding skills.  

But when time is tight and if used sparingly, they are an extremely useful way of avoiding getting slowed down looking for a technically perfect solution and producing one that gets the job done in the least amount of time.

Here's an example of a SQL query that I wrote for my client who sells student accommodation in London, but a bit of background on their systems would probably be helpful here.

When I was working for a digital agency, we worked with the client to implement an integrated, web-based platform that would help their guests find and book rooms via their website but also help their internal teams manage the sales and operational processes of lead tracking, invoice generation, property management and room allocation.

The final solution used a combination of the off-the-shelf, but highly customisable products such as [WordPress](https://www.wordpress.org/), [SugarCRM](https://www.sugarcrm.com/) and [Xero](https://www.xero.com/) together with a completely bespoke interface built using [ReactJS](https://reactjs.org/) (see image below).

_Bespoke interface built in ReactJS –_
![Student Accommodation Room Allocation Interface](post_images/2021-09-07-blog_post_react_interface.png)

Every July the platform needs preparing for the new intake of students coming to study in London in September.  But things never run smoothly and the client often finds our the week before guests are due to arrive, which room numbers they have been allocated by the property.

To work around this issue, we assign temporary room numbers to the room records with SugarCRM from the start, allowing the client to begin allocating bookings to rooms as early as possible but, when the real room numbers arrive, the database needs updating.

There are effectively two database tables that are affected by this change in room numbers, `rooms` (obviously) and `availability` (containing a record for each night each room is available).  

Rather than write a query or script that updates both tables at the same time, I wrote the query below (_Query #1_) that finds all the availability related to the rooms where the room number has changed and use it to output a set of smaller queries (_Query #2_) that can be run as a bloc to update the `name` field on the `availability` table. 

_Query #1 –_
```
SELECT 
`availability`.`id`,
`availability`.`name` AS `name_old`,
CONCAT(`rooms`.`name`,RIGHT(`availability`.`name`,12)) AS `name`,
CONCAT("UPDATE `availability` SET `name` = '",
CONCAT(`rooms`.`name`,RIGHT(`availability`.`name`,12)),
"' WHERE `id` = '",`availability`.`id`,"';") AS `sql_update_query`
FROM `availability`
INNER JOIN `availability_cstm` ON
`availability`.`id` = `availability_cstm`.`id_c`
INNER JOIN `rooms_availability_1_c` ON
`rooms_availability_1_c`.`rooms_availability_1availability_idb` = `availability`.`id`
INNER JOIN `rooms` ON
`rooms_availability_1_c`.`rooms_availability_1rooms_ida` = `rooms`.`id`
INNER JOIN `rooms_cstm` ON
`rooms`.`id` = `rooms_cstm`.`id_c`
WHERE `rooms_availability_1_c`.`rooms_availability_1_rooms_ida` 
= '7c7b4947-4965-44f0-8301-da4731aa0478'
```
_Query #2 –_
```
UPDATE `availability` SET `name` = 'Room A-302A - Canvas Walthamstow - 01 Apr 22' WHERE `id` = '7fba8770-fd1a-11eb-8899-06678b355ec6';
UPDATE `availability` SET `name` = 'Room A-302A - Canvas Walthamstow - 01 Aug 22' WHERE `id` = '84126da6-fd1a-11eb-a0b9-06678b355ec6';
UPDATE `availability` SET `name` = 'Room A-302A - Canvas Walthamstow - 01 Dec 21' WHERE `id` = '7bb16c98-fd1a-11eb-ba02-06678b355ec6';
UPDATE `availability` SET `name` = 'Room A-302A - Canvas Walthamstow - 01 Feb 22' WHERE `id` = '7de3fb52-fd1a-11eb-92e3-06678b355ec6';
UPDATE `availability` SET `name` = 'Room A-302A - Canvas Walthamstow - 01 Jan 22' WHERE `id` = '7cfc3998-fd1a-11eb-b520-06678b355ec6';
```