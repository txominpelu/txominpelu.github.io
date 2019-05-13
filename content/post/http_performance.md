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
to measure the performance of a web service and then  

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
[How to measure network performance metrics via passive traffic analysis?](https://accedian.com/enterprises/blog/measure-network-performance-metrics-passive-traffic-analysis/)

