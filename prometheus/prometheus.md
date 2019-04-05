- [Help wanted](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)

- [Low Hanging Fruit](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22low+hanging+fruit%22)

- [irc](https://riot.im/app/#/room/#prometheus:matrix.org)

#### Pre-reqs
- [x] [Contributing Doc](https://github.com/prometheus/prometheus/blob/master/CONTRIBUTING.md)
- [x] [Code of Conduct](https://github.com/cncf/foundation/blob/master/code-of-conduct.md)


#### Official Docs
- [ ] [godoc](https://godoc.org/github.com/prometheus/prometheus)
- [x] [Internal Architecture](https://github.com/prometheus/prometheus/blob/master/documentation/internal_architecture.md)
  - the server [runs all components](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L366-L598) in an [actor-like model](https://www.brianstorti.com/the-actor-model/), using [`github.com/oklog/oklog/pkg/group`](https://godoc.org/github.com/oklog/run) to coordinate the startup and shutdown of all interconnected actors. Multiple channels are used to enforce ordering constraints, such as not enabling the web interface before the storage is ready and the initial configuration file load has happened.
 - [x] [Service Discovery Docs](https://github.com/prometheus/prometheus/blob/master/discovery/README.md) 
   - Prometheus will call the Run() method on a provider to initialize the discovery mechanism. The mechanism will then send all the target groups into the channel. Now the mechanism will watch for changes. For each update it can send all target groups, or only changed and new target groups, down the channel. Manager will handle both cases.
- [x] [swagger](https://github.com/prometheus/prometheus/blob/master/documentation/dev/api/swagger.json) 

---
 #### This seems like a good entry issue: https://github.com/prometheus/prometheus/issues/4935

It's good because it will help me understand how to setup Prometheus locally and how to run tests.
This appearst to be where the issue is: https://github.com/prometheus/prometheus/blob/master/notifier/notifier_test.go#L307

-- Decided to drop this one. It's too flakey to reproduce. On the plus I have the project running locally. So it was still a good intro to the project. Checking out a new issue...

--- 

#### This seems like an interesting fullstack issue: https://github.com/prometheus/prometheus/issues/3843 

Currently, the Service Discovery WebUI page lists targets found by service discovery.

I think it'd be useful if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery...

so first thing to understand is what is meant by:

"if it also listed any Alertmanager targets found if the alertmanager configuration uses service discovery"

- [x] how do I setup SD in the alert manager? https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery

- [x] setup local dev environment by adding alert manager... found this: https://github.com/vegasbrianc/prometheus will replace https://github.com/danielbh/prom-go-example 

- [x] How can I visually see that alert manager is successfully connected? It shows up in http://localhost:9090/status under "Alertmanagers" It does the same thing when you do service discovery.

- [x] setup sd with alert manager locally to observe difference in UI https://prometheus.io/docs/prometheus/latest/migration/#alertmanager-service-discovery see if you can combine it with... https://prometheus.io/docs/guides/file-sd/

- [x] Observe how targets are added to the UI. This is important because we must understand how they are added so that we can cleanly add the alertmanager sd targets.
  - [here is http handler for web UI of /service-discovery](https://github.com/prometheus/prometheus/blob/master/web/web.go#L287) where it is wrapped in a readf() to test if it's ready
  - the generic handler used in this project is defined [here](https://github.com/prometheus/prometheus/blob/master/web/web.go#L667): It is mounted on a [handler struct](https://github.com/prometheus/prometheus/blob/master/web/web.go#L115), which creates a rather interesting pattern. Each http handler is mounted on this struct which makes available many great utilities, this includes things like, managers (ruleManager, scrapManager): that "manage important functionality". There there is an [options struct](https://github.com/prometheus/prometheus/blob/master/web/web.go#L165) that can be added to this to add config to the handler instance. In any case a lot of time could be spent here as the code here is quite dense.
  
  - [Here is a good example of a Prometheus config](https://github.com/danielbh/prometheus-docker-compose/blob/master/prometheus/prometheus.yml#L28)
  
  #### ServiceDiscovery handler
  - From here we can observe the actual ServiceDiscovery handler. 
      - [It grabs all sd targets](https://github.com/prometheus/prometheus/blob/master/web/web.go#L669)
      - [appends each job](https://github.com/prometheus/prometheus/blob/master/web/web.go#L671)
      - [creates a struct literal called scrapeConfigData](https://github.com/prometheus/prometheus/blob/master/web/web.go#L674)
      - [populates scrapConfigData for the html template data to be rendered](https://github.com/prometheus/prometheus/blob/master/web/web.go#L687-L703)
      - [passes populated scrapConfigData struct into the html template](https://github.com/prometheus/prometheus/blob/master/web/web.go#L705)
      
      - Lets have deeper look at the sd handler and and clarify what a few variables actually are and why they are shaped the way they are.
        - *target*:In this context a better name might have been *scrape_configs*. When Prometheus scrapes a target, it attaches some labels automatically to the scraped time series which serve to identify the scraped target. 
        - *job*: The configured job name that the target belongs to. In Prometheus terms, an endpoint you can scrape is called an instance, usually corresponding to a single process. A collection of instances with the same purpose, a process replicated for scalability or reliability for example, is called a job.
        - *instance*: part of the target's URL that was scraped.
        - *active*: If there are labels for a target it is considered active
        - *dropped*: If there are no labels for a target it is considered dropped
        - *total*: number of targets for a job
        
     - [here is the service-discovery template](https://github.com/prometheus/prometheus/blob/master/web/ui/templates/service-discovery.html)
   
  #### scrapeManager
  
  - [This is where we get our "targets" to be rendered](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go#L209)
  - *TargetsAll* is part of [Manager struct](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go#L58) which includes some interested utlities. There is a mutex, scrapeConfigs, scrapePools, among other items.
  - A mutex is created at the beginning of *TargetsAll* signalling we will be doing concurrency in here.
  - Then it makes a map  of keyed strings with an array of pointers to Targets matchint he length of the current scrapePools.
  - Then it populates each target keying by "target set name" and appends activeTargets and Dropped targets.
  
  - Lets go deeper into defining what some of these terms mean
     - [scrapePool](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go#L121): This is appears to be scraping job run in parallel to get metrics. There is a lot going on here. Will go deeper eventually, only will go now if needed to solve current problem.
     - *tset*: Target set map. key is set name. Value is a targetgroup.Group
     - [targetgroup](https://github.com/prometheus/prometheus/blob/master/discovery/targetgroup/targetgroup.go): A set of targets with a common label set(production , test, staging etc.). It also looks like this is the entitly that converts yaml and json to in memory entities. 
        - *Targets*: A list of targets identified by a label set. Each target is uniquely identifiable in the group by its address label. 
        - *Labels*: A set of labels that is common across all targets in the group.
        - *Source*: An identifier that describes a group of targets
        - *scrapeConfig*:
     - [New target sets are added in the manager.Run function](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go#L79)
     - It appears each discovery submodule implements their own Yaml/JSON marshalling depending on it's purpose.
     - Just found this nugget after poking around: [Service Discovery Readme](https://github.com/prometheus/prometheus/blob/master/discovery/README.md)
     
   #### serviceDiscovery Manager

   - [This is where each service discovery config kicks of a new service discovery provider](https://github.com/prometheus/prometheus/blob/master/discovery/manager.go#L320-L424)

   Based on [internal architecture diagram](https://github.com/prometheus/prometheus/blob/master/documentation/images/internal_architecture.svg) It seems that alert manager service discovery is generated via "Notifier" discovery. This means it might be connected completely differently then normal service discovery, and might need UI customization. Need to investigate further.
   
  #### exploring notifier
  
  - The notifier is a notifier.Manager that takes alerts generated by the rule manager via its Send() method, enqueues them, and forwards them to all configured Alertmanager instances. The notifier serves to decouple generation of alerts from dispatching them to Alertmanager (which may fail or take time).
  - The notifier discovery manager is a [`discovery.Manager`](https://github.com/prometheus/prometheus/blob/v2.3.1/discovery/manager.go#L73-L89) that uses Prometheus's service discovery functionality to find and continuously update the list of Alertmanager instances that the notifier should send alerts to. It runs independently of the notifier manager and feeds it with a stream of target group updates over a [synchronization channel](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L587).
  - definition of [disoveryManagerNotify](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L242-L243)
  - It is passsed a context for notify. [Context is found here](https://golang.org/pkg/context/). 
  - It is [implemented here](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L409-L419) and given cancel when it is terminated
  - [we apply the discoveryManagerNotify config here](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L326)
  - [config is generated here](https://github.com/prometheus/prometheus/blob/v2.3.1/cmd/prometheus/main.go#L317-L325)
  
  #### plan to interfrate notifier sd into UI

  - [ ] Make a plan that is aligned with desired spec of displaying in UI
  
    A plan is beginning to form on how I would integrate this. First let's start with the files concernet that give insight into the implementation that would be most consistent with existing patterns.
  
  - [/scrape/manager.go](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go)
    - [TargetsAll()](https://github.com/prometheus/prometheus/blob/master/scrape/manager.go#L209)
  - [/scrape/scrape.go](https://github.com/prometheus/prometheus/blob/master/scrape/scrape.go)
    - [ActiveTargets()](https://github.com/prometheus/prometheus/blob/master/scrape/scrape.go)
    - [DroppedTargets()](https://github.com/prometheus/prometheus/blob/master/scrape/scrape.go#L249)
  - [/notifier/notifier.go](https://github.com/prometheus/prometheus/blob/master/notifier/notifier.go)
    - [DroppedAlertManagers()](https://github.com/prometheus/prometheus/blob/master/notifier/notifier.go#L432)
    - [AlertManagers()](https://github.com/prometheus/prometheus/blob/master/notifier/notifier.go#L413)
  - [/web/web.go](https://github.com/prometheus/prometheus/blob/master/web/web.go)
    - [ServiceDiscovery()](https://github.com/prometheus/prometheus/blob/master/web/web.go#L668)
  - [/web/ui/templates/service-discovery.html](https://github.com/prometheus/prometheus/blob/master/web/ui/templates/service-discovery.html)
  
  There appears to be parity between scrape.go and notifier.go wrt exporting Dropped and Active "Targets" even though they aren't called the same thing. It would make sense to have a function called AlertManagersAll() that exports all the active and dropped targets in the same way TargetsAll() does. At that point they can be merged into the data model that is then merged into the service-discovery html template.
  
  - [ ] backend
  - [ ] frontend
  - [ ] submit PR
