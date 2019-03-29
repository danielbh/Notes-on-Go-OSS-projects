- [Help wanted](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)

- [Low Hanging Fruit](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22low+hanging+fruit%22)

- [irc](https://riot.im/app/#/room/#prometheus:matrix.org)

#### Pre-reqs
- [x] [Contributing Doc](https://github.com/prometheus/prometheus/blob/master/CONTRIBUTING.md)
- [x] [Code of Conduct](https://github.com/cncf/foundation/blob/master/code-of-conduct.md)

This seems like a good entry issue: https://github.com/prometheus/prometheus/issues/4935

It's good because it will help me understand how to setup Prometheus locally and how to run tests.
This appearst to be where the issue is: https://github.com/prometheus/prometheus/blob/master/notifier/notifier_test.go#L307

-- Decided to drop this one. It's too flakey to reproduce. On the plus I have the project running locally. So it was still a good intro to the project. Checking out a new issue...

This seems like an interesting fullstack issue: https://github.com/prometheus/prometheus/issues/3843 

Currently, the Service Discovery WebUI page lists targets found by service discovery.

I think it'd be useful if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery...

so first thing ot understand is what is meant by:

"if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery"

- [x] how do I setup SD in the alert manager? https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery

- [x] setup local dev environment by adding alert manager... found this: https://github.com/vegasbrianc/prometheus will replace https://github.com/danielbh/prom-go-example 

- [ ] setup sd with alert manager locally to observe difference in UI https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery

- [ ] Make a plan that is aligned with desired spec of displaying in UI
- [ ] backend
- [ ] frontend
- [ ] submit PR
