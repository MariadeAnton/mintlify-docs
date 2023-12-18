---
title: Data model
description: Understanding the Valued data model, and how to use it to your advantage.
---

## Overview

The core of the Valued data model is based on [events](#events), [customers](#customers), and [users](#users), scoped to your [project](#projects).

{% figure src="/images/core-data-model.svg" /%}

This data model allows us to better understand your users, the customer accounts they belong to, and the actions they take within your product.

Modern B2B SaaS products support users belonging to multiple customer accounts, and this is reflected in our [event](#events) data modeling. 

We also support the concept of [customer events](#customer-events) as we understand that not all events are associated with a user, For example, if you are extending the trial of a customer, you only need to associate that event with the customer.

---

## Core models

### Events

To understand feature adoption by your [customers](#customers) and [users](#users) you will need to capture events that represent feature usage and activity. This includes [action](/core/event-definitions#action-events) and [page view](/core/event-definitions#page-view-events) activity throughout your product.

To update information related to a customer or user you can send a [sync](/core/event-definitions#sync-events) to Valued.

More information on the different events you can send to Valued, along with full specifications can be found on the [Event Definitions](/core/event-definitions) page.

---

### Customers

Customers are a top-level grouping of one or more users. This enables your team to understand how feature adoption and usage activity happens on a customer account level. 

Customer accounts can include personal accounts, team accounts, company accounts, and organization accounts.

Additionally, we support [users](#users) belonging to more than one customer, giving you the flexibility to model team membership as your product models it.

As an example, we recommend capturing and [syncing](/core/event-definitions#sync-events) the following information:

```
id: 123
name: "Highly Valued, Inc."
email: "hello@valued.app"
website: "https://valued.app"
location:
  country: "USA"
  state: "California"
plan_type: "free"
```

---

### Users

Valued focuses on identifiable (registered) users within your product. Each user requires a unique id in your product which can be used to identify the user on Valued. 

Users can belong to more than one [customer](#customers), giving you the flexibility to model user membership as your product models it, and the power to understand which customer account they are most active in.

As an example, we recommend capturing and [syncing](/core/event-definitions#sync-events) the following information:

```
id: 123
name: "Josh Kalderimis"
email: "josh@valued.app"
location:
  country: "NZ"
  region: "Wellington"
avatar_url: "https://avatars.bigcompany.com/josh.png"
```

---

### User Sessions

As we are processing the events stream, we create a `session` to group together [action](/core/event-definitions#action-events) and [page view](/core/event-definitions#page-view-events) events that are associated with a [user](#users). 

Events are added to a `session` when the gap between the last event for the session and the `occurred_at` timestamp of the current event is less than 30 minutes.

`User sessions` can group [action](/core/event-definitions#action-events) and [page view](/core/event-definitions#page-view-events) events that span different [customers](#customers)](#customers).

For example, if a `user` views a page, then 5 minutes later performs an `action` (e.g. generating a report), we will create a `session` that groups those two events. If 25 minutes later the user views another page we will add that event to the previous session as it is under the 30-minute cutoff.

But if the user then comes back to your application two hours later and generates another report, or views a few more pages, we will create another `session` as the gap between the last event and the current event is more than 30 minutes.

{% figure src="/images/user-sessions.svg" /%}

---

### Customer Sessions

Similar to how we create [user sessions](#user-sessions), we also create a `customer session` that groups together [action](/core/event-definitions#action-events) and [page view](/core/event-definitions#page-view-events) events that are associated with a [user](#users) and [customer](#customer).

Events are added to a `session` when the gap between the last event for the session and the `occurred_at` timestamp of the current event is less than 30 minutes.

For example, if a [user](#users) views a page for **Customer A**, then 5 minutes later views another page that is associated with **Customer B**, along with performing an `action` (e.g. generating a report) for **Customer B**, we will create a [user session](#user-sessions) that groups those three events, plus two `customer sessions` which groups the events for **Customer A** and **Customer B**.

If 25 minutes later the [user](#users) views another page for **Customer B** we will add that event to the previous [user session](#user-sessions) and `customer session` as it is under the 30-minute cutoff.

But if the [user](#users) then comes back to your application two hours later and generates another report, or views a few more pages, we will create another `user session` and `customer session` as the gap between the last event and the current event is more than 30 minutes.

{% figure src="/images/customer-sessions.svg" /%}

---

## Use cases

### Customer events
