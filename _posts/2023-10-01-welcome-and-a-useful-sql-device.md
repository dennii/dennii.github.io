---
layout: post
title: Welcome, and a useful SQL device
---

Welcome to my blog! You can learn more about me in the [About]({{ site.url }}{% link about.md %}) section, or view a
detailed CV [here]({{ site.url }}{% link cv.md %}). But in short:

- I like programming.
- I like maths.
- I (sometimes) like data.

This blog will be my avenue for sharing some things I find useful or interesting, and hopefully you feel the same way.
Since a purely "welcome" post would be pretty dull, I'll start the blog off by sharing with you a T-SQL device that can
come in useful for helping to structure complex queries.

## Local variables in SQL expressions

Not, I'm not talking about the likes of `declare @var int` in T-SQL or `declare var int` in PostgreSQL (or PL/SQL). I
mean a means of aliasing expressions in a SQL query, so you can refer to them by name rather than duplicating the
expression in multiple places.

To illustrate, let's suppose we have some data representing the GPS positions of a vehicle. (I've done extensive
analytics engineering with this sort of telematics data, so it tends to pop into mind when I'm thinking of examples.)
The data is in a table that contains:

- An `id` field identifying each position.
- A `vehicle_id` field indicating which vehicle the GPS position is for.
- Three fields to hold the time and coordinates of the position: `timestamp`, `latitude`, and `longitude`. Typically
  latitudes and longitudes are given in decimal form, so we'll assume that's the case here.

As a SQL DDL:

```sql
create table db.positions (
  id         int,
  vehicle_id int,
  timestamp  timestamp,
  latitude   float,
  longitude  float
);
```

What I have in mind is to calculate the point-to-point distance, hence the (average) speed from one point to the next.
The distance is approximated by the [Haversine formula](http://en.wikipedia.org/wiki/haversine_formula) for the
shortest distance between two points on a sphere:

$$
  \text{d}(P, Q)
    = 2r \arcsin\left(\sqrt{\sin^2\left(\tfrac{\varphi_2 - \varphi_1}{2}\right)
    + \cos \varphi_1 \cdot \cos \varphi_2 \cdot \sin^2\left(\tfrac{\lambda_2 - \lambda_1}{2}\right)}\right)
$$

where $P = (\varphi_1, \lambda_1)$ and $Q = (\varphi_2, \lambda_2)$ are two positions expressed in radians
(as $(\text{latitude},\,\text{longitude})$) and $r$ is the radius of the sphere under consideration. We'll take
$r = 6371\,\text{km}$, which is roughly the radius of the Earth. Of course, the Earth isn't really a sphere, so this
will just be an approximation---albeit a pretty accurate one.

To simplify things, let's assume we've defined a Haversine SQL function:

```sql
haversine(lat_1, lon_1, lat_2, lon_2)
```

Armed with the above function, we can calculate the point-to-point speed of the vehicle at each position. Now typically
this would be done in one of two ways:

1. By using sub-queries (this will get messy very quickly).
2. By using CTEs.
3. By repeating (sub-)expressions.

(Or some combination of the three.)

Another approach---the one I'm proposing in this post---is to use `cross apply` (also known as a `lateral join`; for
instance, in Postgres). It looks like this:

```sql
select
  pos.id,
  pos.vehicle_id,
  kmh.kmh_to_next_position
from db.positions pos
cross apply (
  -- Convert latitude and longitudes to radians.
  select
    radian(latitude ) as latitude,
    radian(longitude) as longitude
) cur
cross apply (
  -- Get the next timestamp and position coordinates.
  select
    lead(pos.timestamp) over (
      partition by pos.vehicle_id
      order by pos.timestamp
    ) as timestamp,
    lead(cur.latitude) over (
      partition by pos.vehicle_id
      order by pos.timestamp
    ) as latitude,
    lead(cur.longitude) over (
      partition by pos.vehicle_id
      order by pos.timestamp
    ) as longitude
) nxt
cross apply (
  select
    haversine(
      cur.latitude, cur.longitude,
      nxt.latitude, nxt.longitude
    ) as km_to_next_position,
    datediff(
      second,
      cur.timestamp,
      nxt.timestamp
    ) / 3600.0 as hr_to_next_position
) tnp
cross apply (
  select
    tnp.km_to_next_position / tnp.hr_to_next_position as kmh_to_next_position
) kmh
```

The calculations are now broken up into a sequence of `cross apply`'s:

- The first one converts the latitudes and longitudes to radians.
- The second gets the required data for the "next" position.
- The third calculates the position-to-position distance and time.
- And lastly, the final one calculates the speed.

I've found this pattern to be especially useful when there are multiple complex formula, depending on the same
sub-expressions (or sub-calculations). The `cross apply` technique ends up much shorter than using CTEs, as well as
being much clearer, because you don't need to drag along the entire table along each time.

Of course, the obvious downside is that the calculations appear _after_ the query that references them---but this
reversal of expected order is par for the course with SQL, so we should all be accustomed to it!
