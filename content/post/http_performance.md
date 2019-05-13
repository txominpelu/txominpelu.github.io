+++
title = "Understanding Http Performance"
tags = [
    "http",
    "performance",
    "development",
]
date = 2019-04-20
toc = true
+++

Imagine this scenario: you just developed a new web service
you deploy it in production and your service starts to get 
traffic. This article provides a simple explanation of how
to measure its performance and then provides tips to help
you identify common scenarios that cause poor performance
and how to fix them.

## Measuring performance

If you have never monitored the performance of web service
a good metric to look at is latency. Latency is "a time delay
between the cause and the effect" -- <cite>[Latency][1]</cite>
or in this context the delay between the request and the 
response.

When looking at the latency just like with any other metrics it is
important to look at percentiles more concretely the higher percentiles
eg: 90%, 99%, 99.9% ... It is a common mistake to only look at the
average. Only looking at the average is problematic because the average only
tells a really small part of the story and your service
can have a decent average but really high latency in higher percentiles. For example
In a user facing service whith high traffic that would mean that many users are
experiencing a really slow website.

The best way to look at percentiles is a grafana dashboard like this:

![Example Grafana Percentiles](/grafana_percentile.png)






Tips:

- Look at percentiles (average doesn't mean much)
- Look at from the client and from the server
    - Log all times from client side (e.g events in okttp)
    - Possible scenarios:
        - Slow server end to end
        - Low wallclock in server but high wallclock on client
            - Server queuing
            - Slow connection / dns / service discovery 
            - Slow network, packet loss, retry (Expect some network failure when looking at high percentiles (e.g packet loss) )
    - Check that the client is doing service discovery properly (e.g maximizing data locality)

- Consider a reverse proxy (to alleviate weight from the server)
- Distribute the charge across servers
- Do things asynchronously when possible (e.g calling other services)
- Batch when possible (batch queries to SQL or services)

[Throughput]().

## Network Performance Metrics

- Latency
    - Connection Time
    - Round Trip Time
- Packet loss
    - Retransmission rates
    - Retransmission delay
- Throughput
    - Throughput
    - Data Transfer Time
- Server Response Time

## Possible causes

- Slow server
- Slow network
- Slow DNS

## Tools to analyze performance - TShark

## References
[1]:https://en.wikipedia.org/wiki/Latency_(engineering)
[How to measure network performance metrics via passive traffic analysis?](https://accedian.com/enterprises/blog/measure-network-performance-metrics-passive-traffic-analysis/)



