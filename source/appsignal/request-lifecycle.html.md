---
title: "The life cycle of an AppSignal request"
---

From the code in your application to our gem, our server and your performance page, there are a lot of moving parts working together to generate our pretty graphs and performance pages. This page will describe the entire process.

AppSignal is eventual consistent, this means that (even though we try to minimize this) there is no arbitrary duration between a request on your server and displayed data on AppSignal.

Sometimes requests sent later than others will appear first on AppSignal. By breaking down the process we can tell you why this happens.

## The gem

When you include the `appsignal` gem in your Gemfile, AppSignal will start running as soon as you start your application. We check for a valid config file and start a thread that does all the heavy lifting. We wrap each incoming request or background job with a "transaction". This transaction collects all instrumentation that's being generated during the request. It also tries to get environment variables, session data and parameters where needed.

After your application serves the request, the transaction is closed and placed into a queue. Depending on your environment (development/production) and the gem version, the thread that was started during the application start sleeps a certain amount of time. Once the sleep is over it processes the queue of transactions and sends it to our AppSignal push api.

Because you can have multiple processes and webservers that all started at a different time, it can happen that a request on one server is sent a few seconds after its transaction has been closed, while on another server it can take up to 60 seconds before this happens. This is the reason AppSignal is eventually consistent.

## The push api

Our push api has one and one purpose only, to receive data and push it onto a queue as fast and efficiently as possible. From here workers process the transaction data from the gem and generate notifications, store samples of slow or faulty requests and push statistical data for each transaction into a "bucket".


## Map/Reduce

Once every few seconds a process starts that takes all the data from the "bucket" of transactions and uses a map/reduce job to generate minutely and hourly stats for the graphs.

## Conclusion

In a worst case scenario it can take up to 90 seconds from request to sample on the performance page. We're constantly trying to minimize this time, but we have to balance the amount of requests our push api receives and data size of each request with processing speed.

If you would like to know more about this subject, don't hesitate to [contact us](mailto:contact@appsignal.com)!
