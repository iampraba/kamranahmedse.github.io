---
layout: post
title: Timezone aware application
comments: true
---

I recently had the chance to work on a web application targetting people from different timezones. To enable the users to have a similar experience, I had to take up the strategy to deal with different timezones. Below is how I went about solving it.

- Store the dates and times in `UTC` format i.e. 0 timezone offset
- On the client side, convert this `UTC` time to user's local timezone using Javascript.

#Timezones and PHP
PHP's `DateTime` is supported in `>=5.2` versions and is really powerful. Below, I show some examples of how to convert from one timezone to another. I am going to take my current timezone into account which is `Asia/Karachi` and is `GMT+5`.

<pre><code class="php">
// Convert from some Timezone to UTC
$clientDateTime = new DateTime('2015-01-19 07:00', new DateTimeZone('Asia/Karachi'));
$clientDateTime->setTimezone(new DateTimeZone('UTC'));
$utcDateTime = $clientDateTime->format("Y-m-d H:i A");

echo $utcDateTime;   // Prints 2015-01-19 02:00 AM
</code></pre>

As you can see the date and time which, according to my timezone (`GMT+5`), is `2015-01-19 07:00` has been converted to `UTC/GMT+0` i.e. 5 hours minus my date which becomes exactly `2015-01-19 02:00 AM`. In a very similar fashion, UTC date can be converted to anyother timezone

<pre><code class="php">
// Convert from some Timezone to UTC
$utcDateTime = new DateTime('2015-01-19 02:00', new DateTimeZone('UTC'));
$utcDateTime->setTimezone(new DateTimeZone('Asia/Karachi'));
$clientDateTime = $utcDateTime->format("Y-m-d H:i A");

echo $clientDateTime;   // Prints 2015-01-19 02:00 AM
</code></pre>

Also, one might want to generate a dropdown or a list of timezones. Instead of grabbing it from somewhere else, I would suggest to have PHP generate that for you. Below is how:

<pre><code class="php">
$timezones = DateTimeZone::listIdentifiers(DateTimeZone::ALL);
</code></pre>

It will return an array consisting of the timezones.

#Timezones and MySQL

Although, I handled the time conversions etc in my application logic, let me state a few points about MySQL here as well. To get timestamp in `UTC`, use `UTC_TIMESTAMP()` instead of `NOW()`. Also, it is recommended that you *always* use `UTC_TIMESTAMP()` rather than `NOW()` for dates etc and the reason for that is `NOW()` returns the current local time of the server and the server can be in any timezone.