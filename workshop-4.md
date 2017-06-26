title: Web development practices
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
.bottom-bar[
  {{title}}
]

---

class: impact

# {{title}}
## Monitoring <3 ChatOps

---
class: middle, center

# Being notified when your app goes down is great

--

# for the end user and not for you

---

# Monitor our app's critical endpoints

* an endpoint where you touch the database
* an endpoint where you touch file system
* an endpoint where you generate the session

## For example:

`curl <yourapp>/healtcheck/db`, where you do `SELECT 1`

---
class: middle, center

# Oooh that's easy, everybody can write an app which does this

--

![](https://cdn.meme.am/cache/instances/folder772/62529772.jpg)

---
class: middle, center

You don't want to wake up at 3 am because your home baked app failed to fetch the page because the data center was restarting their router.

???
search for `monitoring sucks` on google

---

# Use a proper health checker solution

* uptimerobot (https://uptimerobot.com/)
* pingdom (https://www.pingdom.com/)
> It even tries to load your app from multiple geo regions

---

# Additional important notes

* get notified
* have an on call list
* the app's lead dev should be responsible too

---

# Incident management

* PagerDuty
* OpsGenie
* VictorOps

> SMS, email, push notification support

---

# Make the incidents public

## Create a channel on HipChat/Slack where you can see who did what with your app

???
Github chatops: https://speakerdeck.com/jnewland/chatops-at-github

---

# When should we get notified?

* `/` returns `500 internal server error`
--
 ✔️
--

* `/` responds in 5 seconds
--
 ✔️
--

* `/` responds in 5 seconds and it's 3 am
--
 ✖️

---

# When should we get notified?

* 1 of 5 sign-ups failed
--
 ✔️

--

* 1 of 5 sign-ups failed and it's 3 am
--
 ✖️

--

* 5 of 5 sign-ups failed
--
 ✔️
--

* 5 of 5 sign-ups failed and it's 3 am
--
 ✔️

???
More example:

* SSL cert will expire in 2 weeks (and you check it on each day at midnight of course)

---

## We should send metrics from our app to the monitoring system
## Set each issues' severity (critical, not critical, etc)

???
Severity example: https://response.pagerduty.com/before/severity_levels/

---

class: middle, center
# So what can we monitor in a chatroom?

* new incident is open
* incident has been acknowledged
* incident has been escalated to a new person
* new commit or pull request
* deployment has been started
* deployment failed/succeeded

---
class: center, middle
# New deployment has been started?

---
class: center, middle

Let's create a bot which does deploy a new version from our app to beanstalk by executing `/build app`

---
class: center, middle
# Before jumping in, how to version?

--

## Semantic versioning: http://semver.org/

---

[koa-robot](https://bitbucket.org/oroce/koa-robot)

---

class: middle, center

# What should happen after we had an incident

---

# Postmortem

* note down the issue and when it happened
* discuss it 2 days later
* don't blame people
* define actionable items

origins: [etsy](https://codeascraft.com/2012/05/22/blameless-postmortems/)

???
* two days later you are less emotional but still the experience just happened so you can be objective while able to recall it

* people you blame will be in fear, so they won't be open

---

class: center, middle

To be able to make post-mortem possible, you need logging. Log everything.
Just put the logs onto S3 and keep trying to reproduce and understand what caused the issue.

---

# For the future

* [Awesome Chatops](https://github.com/exAspArk/awesome-chatops)
* [Github chatops](https://speakerdeck.com/jnewland/chatops-at-github)
* [Pageduty alerting principals](https://response.pagerduty.com/oncall/alerting_principles/)
