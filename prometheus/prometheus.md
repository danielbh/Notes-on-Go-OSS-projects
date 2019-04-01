- [Help wanted](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)

- [Low Hanging Fruit](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22low+hanging+fruit%22)

- [irc](https://riot.im/app/#/room/#prometheus:matrix.org)

#### Pre-reqs
- [x] [Contributing Doc](https://github.com/prometheus/prometheus/blob/master/CONTRIBUTING.md)
- [x] [Code of Conduct](https://github.com/cncf/foundation/blob/master/code-of-conduct.md)

---
 #### This seems like a good entry issue: https://github.com/prometheus/prometheus/issues/4935

It's good because it will help me understand how to setup Prometheus locally and how to run tests.
This appearst to be where the issue is: https://github.com/prometheus/prometheus/blob/master/notifier/notifier_test.go#L307

-- Decided to drop this one. It's too flakey to reproduce. On the plus I have the project running locally. So it was still a good intro to the project. Checking out a new issue...

--- 

#### This seems like an interesting fullstack issue: https://github.com/prometheus/prometheus/issues/3843 

Currently, the Service Discovery WebUI page lists targets found by service discovery.

I think it'd be useful if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery...

so first thing ot understand is what is meant by:

"if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery"

- [x] how do I setup SD in the alert manager? https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery

- [x] setup local dev environment by adding alert manager... found this: https://github.com/vegasbrianc/prometheus will replace https://github.com/danielbh/prom-go-example 

- [ ] setup sd with alert manager locally to observe difference in UI https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery

- [ ] Observe how targets are added to the UI.
  - [here is http handler for web UI of /service-discovery](https://github.com/prometheus/prometheus/blob/master/web/web.go#L287) where it is wrapped in a readf() to test if it's ready
  - the generic handler used in this project is defined [here](https://github.com/prometheus/prometheus/blob/master/web/web.go#L667): It is mounted on a [handler struct](https://github.com/prometheus/prometheus/blob/master/web/web.go#L115), which creates a rather interesting pattern. Each http handler is mounted on this struct which makes available many great utilities, this includes things like, managers (ruleManager, scrapManager): that "manage important functionality". There there is an [options struct](https://github.com/prometheus/prometheus/blob/master/web/web.go#L165) that can be added to this to add config to the handler instance. In any case a lot of time could be spent here as the code here is quite dense.
  - From here we can observe the actual ServiceDiscovery handler. 
      - [It grabs all sd targets](https://github.com/prometheus/prometheus/blob/master/web/web.go#L669)
      - [appends each job](https://github.com/prometheus/prometheus/blob/master/web/web.go#L671)
      - [creates a struct literal called scrapeConfigData](https://github.com/prometheus/prometheus/blob/master/web/web.go#L674)
      - [populates scrapConfigData for the html template data to be rendered](https://github.com/prometheus/prometheus/blob/master/web/web.go#L687-L703)
      - [passes populated scrapConfigData struct into the html template](https://github.com/prometheus/prometheus/blob/master/web/web.go#L705)
  

- [ ] Make a plan that is aligned with desired spec of displaying in UI
- [ ] backend
- [ ] frontend
- [ ] submit PR
