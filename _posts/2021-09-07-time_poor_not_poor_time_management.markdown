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

## Google is your friend (and foe)

It's an incredibly obvious tool but I have found myself using Google to resolve a problem almost daily.  

I might be knee deep in code, attempting to execute a task and I either can't remember how to do something or it's something completely brand new to me.  

Either way, rather than eat up valuable time trying to figure it out myself, the result of a Google search will quickly get me on a path towards a solution.

Unfortunately, whilst there is a huge amount of helpful technical content out there on the internet, there is almost an equal amount of unhelpful or incorrect technical content.  After a while though, you begin to build up a sense of those sources and websites that can be trusted and those that can't, or at least should be viewed with a sense of caution.

## Don't be afraid to ask for help

## Simple solutions are fast and cost effective... 

But they won't necessarily expand your coding skills.  

I affectionately refer to these as "gaffer tape solutions", they get the job done allbeit in a slightly ugly fashion. 

But when time is tight and if used sparingly, they are an extremely useful way of avoiding getting slowed down looking for a technically perfect solution and producing one that gets the job done in the least amount of time.

Here's an example of a SQL query that I wrote for my client who sells student accommodation in London, but a bit of background on their systems would probably be helpful here.

When I was working as Chief Technical Officer for a digital agency, we worked with the client to implement an integrated, web-based platform that would help their guests find and book rooms via their website but also help their internal teams manage the sales and operational processes of lead tracking, invoice generation, property management and room allocation.

The final solution used a combination of the off-the-shelf, but customisable products such as [WordPress](https://www.wordpress.org/), [SugarCRM](https://www.sugarcrm.com/) and [Xero](https://www.xero.com/) together with a completely bespoke interface built using [ReactJS](https://reactjs.org/) (see image below).

![Student Accommodation Room Allocation Interface](images/2021-09-07-blog_post_react_interface.png)


```
SELECT 
`lnavy_availability`.`id`,
`lnavy_availability`.`name` AS `name_old`,
CONCAT(`lnrom_rooms`.`name`,RIGHT(`lnavy_availability`.`name`,12)) AS `name`,
CONCAT("UPDATE `lnavy_availability` SET `name` = '",
CONCAT(`lnrom_rooms`.`name`,RIGHT(`lnavy_availability`.`name`,12)),
"' WHERE `id` = '",`lnavy_availability`.`id`,"';") AS `sql_update_query`
FROM `lnavy_availability`
INNER JOIN `lnavy_availability_cstm` ON
`lnavy_availability`.`id` = `lnavy_availability_cstm`.`id_c`
INNER JOIN `lnrom_rooms_lnavy_availability_1_c` ON
`lnrom_rooms_lnavy_availability_1_c`.`lnrom_rooms_lnavy_availability_1lnavy_availability_idb` = `lnavy_availability`.`id`
INNER JOIN `lnrom_rooms` ON
`lnrom_rooms_lnavy_availability_1_c`.`lnrom_rooms_lnavy_availability_1lnrom_rooms_ida` = `lnrom_rooms`.`id`
INNER JOIN `lnrom_rooms_cstm` ON
`lnrom_rooms`.`id` = `lnrom_rooms_cstm`.`id_c`
WHERE `lnrom_rooms_lnavy_availability_1_c`.`lnrom_rooms_lnavy_availability_1lnrom_rooms_ida` 
= '7c7b4947-4965-44f0-8301-da4731aa0478'
```

