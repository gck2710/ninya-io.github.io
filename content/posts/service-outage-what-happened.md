---
title: "service outage: what happened"
date: "2014-05-18"
categories:
    - "ninya.io"
---

On 2014-05-17 we received an automated email from our elasticsearch provider [found.no](http://www.found.no) who informed us that
our elasticsearch instance ran out of memory and was restarted. Unfortunately this email did not directly rang the alarm sirens on our end
as we had this a couple of times before without causing any inconvenience.

We noticed the service was unavailable soon. We were able to quickly narrow it down to our elasticsearch index being corrupted.
Since we don't host our own elasticsearch but instead rely on the awesome service of [found.no](http://www.found.no), we immediately
reached out to their support.

Fortunately they were able to restore everything within minutes.

They also told us that our elasticsearch instance was badly in need of more memory and that the same problem will happen again if we
don't provide it with more memory.


### What do we do to make sure this won't happen again?


1.we immediately doubled the amount of memory of our elasticsearch instance

2.we will install email/sms warnings to get notified about outages quicker

3.we will provide a http://status.ninya.io site for everyone to quickly see the status of the service


### How can you help us to make the service more reliable?

[ninya.io](http://www.ninya.io) is a free open source service. It's currently backed with a small private hobby budget. If you like to become a sponsor
please drop an email to <christoph.burgdorf@gmail.com>. Alternatively you might leave a [gittip](https://www.gittip.com/cburgdorf/)
if that's more your thing.
