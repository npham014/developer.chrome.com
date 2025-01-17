---
layout: "layouts/blog-post.njk"
title: Options of a PushSubscription
description: >
  You can now access the options used when subscribing a user to push.
authors:
  - mattgaunt
date: 2016-09-07
updated: 2016-09-05
---

When a `pushsubscriptionchange` event occurs, it's an opportunity for a developer
to re-subscribe the user for push. One of the pain points of this is that to
re-subscribe a user, the developer has to keep the `applicationServerKey` (and any
other `subscribe()` options) in sync between the web page's JavaScript and their
service worker.

In Chrome 54 and later you can now access the options via the options parameter
in a subscription object, known as
[PushSubscriptionOptions](https://www.w3.org/TR/push-api/#pushsubscriptionoptions-dictionary).

You can copy and paste the following code snippet into
[simple-push-demo](https://gauntface.github.io/simple-push-demo/) to see what
the options look like. The code simply gets the current subscription and prints
out `subscription.options`.


```js
navigator.serviceWorker.ready.then(registration => {  
    return registration.pushManager.getSubscription();  
})  
.then(subscription => {  
    if (!subscription) {  
    console.log('No subscription 😞');  
    return;  
    }

    console.log('Here are the options 🎉');  
    console.log(subscription.options);  
});
```    

With this small piece of information you can re-subscribe a user in the
[pushsubscriptionchange](https://www.w3.org/TR/push-api/#the-pushsubscriptionchange-event)
event like so:

```js
self.addEventListener('pushsubscriptionchange', e => {  
    e.waitUntil(registration.pushManager.subscribe(e.oldSubscription.options)  
    .then(subscription => {  
        // TODO: Send new subscription to application server  
    }));  
});
```    

It's a small change, that will be super useful in the future.


