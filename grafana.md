Grafana looks like a good first project to get more exposure into Go. 
Especially because the majority of it is written in typescript. Additionally Grafana is a very popular project with a lot of companies using it as a dashboard.

[Help Wanted Labels for Grafana](https://github.com/grafana/grafana/labels/help%20wanted)
[Beginner Friendly](https://github.com/grafana/grafana/labels/beginner%20friendly)

Packages I'd like to understand:

[bus package](https://github.com/grafana/grafana/tree/b47a4954c9b0208869298c75e97c212430565f1e/pkg/bus): This some type of event bus. Interesting use of interfaces and reflection. I love that it's four years old, but the syntax is not out of date (AFAICT). [Created a fork here for experimenting](https://github.com/danielbh/go-event-bus/tree/master). This package appears to be central to grafana as seen in this [search for "bus" in the grafana repo](https://github.com/grafana/grafana/search?utf8=%E2%9C%93&q=bus&type=). Central to the bus package is reflect and context packages.They instantiate a single instance of a "global bus". This instance is persisted across all packages. [It also looks like they do not like this](https://github.com/grafana/grafana/blob/master/pkg/bus/bus.go#L67). Funny how its been used for 4 years, and the last mention of it not being good in the comment was 7 months ago.

- [repl.it for reflect struct example they are using for messages](https://repl.it/@danielbh/reflect-struct)
- [repl.it for reflect func params example they are using for event listener params](https://repl.it/@danielbh/reflect-func-param)
- [repl.it for reflect context.Background()](https://repl.it/@danielbh/reflect-context)

*** 

This looks like a good first issue to check out: https://github.com/grafana/grafana/issues/13924

*Update* Someone solved this with an MR: https://github.com/grafana/grafana/pull/14110. Looks like it was an easy one should have just gone for it. There will be others.

This will require a quick review of oauth and some other research: 

- [x] https://oauth.net/2/
- [ ] https://github.com/golang/oauth2
- [ ] https://medium.com/@darutk/diagrams-and-movies-of-all-the-oauth-2-0-flows-194f3c3ade85

configure oauth for grafana to test: http://docs.grafana.org/auth/google/

***
 
Looking for new issues...
 
 - ~https://github.com/grafana/grafana/issues/14076~
 - ~https://github.com/grafana/grafana/issues/13612 (This one suggests there is plugin work that needs to be done)~
-  https://github.com/grafana/grafana/issues/13055

*** 

Security issue when resetting password: https://github.com/grafana/grafana/issues/14076

This one looks very straightforward. 

From Issue: 
> The admin password is being set on a command line argument. This command might be saved on bash history and it can be a security issue. We could instead get the password from stdin.

- [ ] Replicate issue
- [x] Go through thought process on how this could be leveraged as a security vulnerabillity. *How you would do this is to exec into that machine as that user then run "history" command and read past commands." This would be an issue in a on an non containerized machine, but could cause issues if you did something like this: https://stackoverflow.com/questions/28279862/docker-and-bash-history . Where you save bash history in a volume. In a containerized environment this could then definitely be a security issue, that would persist past the lifetime of the container. I don't think you would want to save the bash history that often in this example but it is still a vector that could be avoided.*

MR in progress: https://github.com/grafana/grafana/pull/14193

*** 

Started looking at [beginner friendly issues](https://github.com/grafana/grafana/labels/beginner%20friendly)

This is great for onboarding to the project. Going to try to knock a few of these out starting with this one: https://github.com/grafana/grafana/issues/14133

This one claims that an HTTP request to pause all alerts is incorrectly documented. It accepts an authenthetication request with username and password instead of a generated Bearer token. This is great, because now it's exposing me to all the different the http api features grafana has. Great opportunity as a side quest to find something that might be missing.

MR submitted: https://github.com/grafana/grafana/pull/14194

*** 

https://github.com/grafana/grafana/issues/11067 under help wanted

Block removing super admin permission from last super admin user...

Able to remove admin user as grafana-admin leaving no users with admin permissions to Block removing super admin permission from last super admin user.

So if there is an API that means there will be logic that might be used in one or more places. I need to find out where this removal logic is and then provide some type of validation to prevent this action from taking place...

http://docs.grafana.org/http_api/admin/#delete-global-user this is a good lead. This will delete a user using basic authentication.

`DELETE /api/admin/users/:id`

https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L95 is the handler which is called by...

https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L95 is the registered route
