Grafana looks like a good first project to get more exposure into Go. 
Especially because the majority of it is written in typescript. Additionally Grafana is a very popular project with a lot of companies using it as a dashboard.

[Help Wanted Labels for Grafana](https://github.com/grafana/grafana/labels/help%20wanted)
[Beginner Friendly](https://github.com/grafana/grafana/labels/beginner%20friendly)

Packages with interesting code patterns:

[bus package](https://github.com/grafana/grafana/tree/b47a4954c9b0208869298c75e97c212430565f1e/pkg/bus): This some type of event bus. Interesting use of interfaces and reflection. I love that it's four years old, but the syntax is not out of date (AFAICT). [Created a fork here for experimenting](https://github.com/danielbh/go-event-bus/tree/master). This package appears to be central to grafana as seen in this [search for "bus" in the grafana repo](https://github.com/grafana/grafana/search?utf8=%E2%9C%93&q=bus&type=). Central to the bus package is reflect and context packages.They instantiate a single instance of a "global bus". This instance is persisted across all packages. [It also looks like they do not like this](https://github.com/grafana/grafana/blob/master/pkg/bus/bus.go#L67). Funny how its been used for 4 years, and the last mention of it not being good in the comment was 7 months ago.

- [repl.it for reflect struct example they are using for messages](https://repl.it/@danielbh/reflect-struct)
- [repl.it for reflect func params example they are using for event listener params](https://repl.it/@danielbh/reflect-func-param)
- [repl.it for reflect context.Background()](https://repl.it/@danielbh/reflect-context)

[guardian package](https://github.com/grafana/grafana/blob/master/pkg/services/guardian/guardian.go) - some voodoo! They implement/compose an interface (DashboardGuardian) with a struct (dashboardGuardianImpl). Since they implement the methods of the DashboardGuardian interface with the struct it's valid.

- [goconvey](https://github.com/smartystreets/goconvey/wiki/Web-UI) They are using goconvey to do testing which has a built in web UI for test browsing. Kept running into "Build Failure" issues. For testing a single test go test -run TestCaseName.

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

#### Issue:

Able to remove admin user as grafana-admin leaving no users with admin permissions to Block removing super admin permission from last super admin user.

#### Able to Replicate?

- [x] setup run from source
- [x] connect to database so you can run queries to help with development - using https://sqlitebrowser.org/
- [x] replicate bug in app (with api)
- [x] verify issue in database
- [x] reset to initial state - delete data folder and run setup script again.

#### Notified grafana maintainers and claim issue:
done


#### Solution:

*Background*

Few things to note. There is a difference between grafana admin and org admin. A grafana admin is a super super user. Currently in the UI you can totally

*UI Behavior*
Ideally there would be a frontend component to this that would prevent someone from removing themselves as a grafana admin as well as an instructive message. A future issue has been noted for the UI.


*API Behavior*

Need to investigate how this works... below is the attached request done in the local development environment that succeeds in removing someone as an admin.

````markdown

Request URL: http://localhost:3000/api/admin/users/1/permissions
Request Method: PUT
Status Code: 200 OK
Remote Address: [::1]:3000
Referrer Policy: no-referrer-when-downgrade

````
*Where does this exist in code?*

- api register: https://github.com/grafana/grafana/blob/master/pkg/api/api.go#L371
- api handler: https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L79
- data layer handler: https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user.go#L500

*proposed solution* 

Do validation on if the user is the last admin and if they are do not allow them to be turned into a peon.


#### Implementation

- [x] test on api handler
   - [x] create admin scenario
   - [x] create mocked test of sqlstore
- [x] fail test
- [x] use error message format of error types to check if should return 400
- [x] make test pass
- [ ] test on data layer
   - [ ] I need to do a query to count users look at orgRemove it does a count query.
   - [ ] Does it use an ORM?
   - [ ] should send m.ErrLastGrafanaAdmin
- [ ] fail test
- [ ] do query to confirm not admin user

#### Acceptance tests

- [ ] Manually try and delete last admin user and fails with correct error message
- [ ] Unit test for deleting user in data layer
- [x] Unit test for deleting user in api layer

#### Future code to understand

- [ ] But isAdmin is camelcase in code not underscore as is inthe database... where is the underscore case defined? 
    - Our journey starts here: https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user.go#L91
    - CreateUserCommand is used: https://github.com/grafana/grafana/blob/master/pkg/models/user.go#L62
    - It is converted by...?

***

New Issue that could be proposed

- [ ] prevent admin user from being deleted from API: 

#### Able to Replicate?

- [x] setup run from source
- [x] connect to database so you can run queries to help with development - using https://sqlitebrowser.org/
- [x] replicate bug in app (with api)
- [x] verify issue in database
- [x] reset to initial state - delete data folder and run setup script again.

#### Notified grafana maintainers and claim issue:
done

#### Is this just frontend or backend? Will errors propogate to front end in UI?
none

#### Solution:

*Background*

*API Behavior*

So if there is an API that means there will be logic that might be used in one or more places. I need to find out where this removal logic is and then provide some type of validation to prevent this action from taking place...

http://docs.grafana.org/http_api/admin/#delete-global-user this is a good lead. This will delete a user using basic authentication.

`DELETE /api/admin/users/:id`

- is the registered route https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L95 
- is the handler https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L95 
- which calls the `DeleteUserCommand` using bus.Dispatch https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L100
- Which triggers the Delete user event handler: https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user.go#L31
- Which is defined: https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user.go#L473
- Whose test is here: https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user_test.go#L132

So probably here would would check to see if the user in the last admin in the table by checking user does not have `is_admin = 1` Confirm this by looking at schema for user table... https://github.com/grafana/grafana/blob/master/pkg/models/user.go#L33

*UI Behavior*

If the last user is attempted to be deleted in grafana you get the following error message: "Cannot remove last organization admin". 
- In the code this is here: https://github.com/grafana/grafana/blob/master/pkg/api/org_users.go#L123. The handler is [removeOrgUserHelper](https://github.com/grafana/grafana/blob/master/pkg/api/org_users.go#L120) This is triggered by 
    - `DELETE /api/org/users/:userId` [handle is registered here](https://github.com/grafana/grafana/blob/9cc6c2128a8cca647e31a2d6e4d41603b9245995/pkg/api/api.go#L181)
    - `DELETE /api/orgs/:orgId/users/:userId` [handle is registered here](https://github.com/grafana/grafana/blob/9cc6c2128a8cca647e31a2d6e4d41603b9245995/pkg/api/api.go#L213)
    
But how is validation done for this to determine its the last user?

A `RemoveOrgUserCommand` is dispatched to event bus then the command is executed in the sql_store service where a function `RemoveOrgUser` is executed where it deletes the user and does clean-up. Validation is done in this function with [implementation: validateOneAdminInOrg](https://github.com/grafana/grafana/blob/9cc6c2128a8cca647e31a2d6e4d41603b9245995/pkg/services/sqlstore/org_users.go#L161) [definition: validateOneAdminInOrg](https://github.com/grafana/grafana/blob/9cc6c2128a8cca647e31a2d6e4d41603b9245995/pkg/services/sqlstore/org_users.go#L205)

*code*:

````go

func validateOneAdminLeftInOrg(orgId int64, sess *DBSession) error {
	// validate that there is an admin user left
	res, err := sess.Query("SELECT 1 from org_user WHERE org_id=? and role='Admin'", orgId)
	if err != nil {
		return err
	}

	if len(res) == 0 {
		return m.ErrLastOrgAdmin
	}

	return err
}

````

- ~Do we need to do a front end part?~ no. frontend is already covered with removeUserFromOrg


#### Implementation

   - [We need validation on the data layer](https://github.com/grafana/grafana/blob/master/pkg/services/sqlstore/user.go#L479) much like it is done in the data layer for [removing admins from organizations](https://github.com/grafana/grafana/blob/9cc6c2128a8cca647e31a2d6e4d41603b9245995/pkg/services/sqlstore/org_users.go#L161)
   - We need to add [validation on the api layer and give a 400](https://github.com/grafana/grafana/blob/master/pkg/api/admin_users.go#L101) like that is [done in remove user from organization api](https://github.com/grafana/grafana/blob/master/pkg/api/org_users.go#L123)

***

UX on toggling admins. The UI should prevent a user from removing themselves as an admin without the need for a backend call.
