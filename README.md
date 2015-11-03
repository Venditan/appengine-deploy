# App Engine Deploy #

Environment and Deployment Manager for **Google App Engine** Applications

Working towards this: http://12factor.net/config

## What is this? Is it different from appcfg.py? ##

This allows you to **deploy the same code/application to multiple target environments** (local, multiple different App Engine projects).

Critically, it also allows you to **manage environment variables distinctly for each deployment target**.

This tool uses `appcfg.py` to actually push the deployments out, but it builds a dynamic command, overriding the target application id and environment variables at deploy time.

### Example deploy.json file ###

Deployment targets and environments are configured in `deploy.json`, usually in your project root.

Here's a quick example where we have different database credentials for alpha and live environments.

```json
{
    "targets": {
        "alpha": {
            "app_id": "myapp-alpha",
            "version": "alpha++",
            "environment": {
                "APP_DB_USER": "root",
                "APP_DB_NAME": "DatabaseName",
                "APP_DB_SOCKET": "/cloudsql/myapp:instance"
            }
        },
        "live": {
            "app_id": "myapp",
            "version": "2",
            "environment": {
                "APP_DB_USER": "root",
                "APP_DB_NAME": "LiveDatabaseName",
                "APP_DB_SOCKET": "/cloudsql/myapp:instance"
            }
        },
    }
}
```

### Local development environment ###

You can configure the environment variables for your local development server in your yaml files like this:

```yaml
env_variables:
  APP_DB_USER: root
  APP_DB_NAME: localdb
```

## Usage ##

Deployment targets configured in `deploy.json` are executed using the `deploy` command, which is made available the `vendor/bin` folder by Composer.

For example, from your project root:

Deploy the **examples** module to the **alpha** target environment

```bash
vendor/bin/deploy --run --module=examples --target=alpha
```

Create a template **deploy.json** file

```bash
vendor/bin/deploy --init
```

Show the planned `appcfg.py` command for a deployment, but do not run it

```bash
vendor/bin/deploy --test --module=default --target=live
```

List the configured deployment targets

```bash
vendor/bin/deploy --verbose targets
```

### Default Module ###

You can use either `app` or `default` to deploy the default App Engine module (which is configured in your `app.yaml` file).

### Targets ###

Each target is a different deployment, like "staging" and "production".

They must be uniquely named.

### Versions ###

If you suffix your version name with `++` then we will auto-increment the version on each deployment. 

In the example above, the first deployment gets `alpha1` and the second `alpha2` and so on.

In order to do this, we have to be able to detect what versions are already running. So, if you delete all your versions, we will start at 1 again.

### Labels ###

You can supply a version label like this:

```bash
vendor/bin/deploy --run --module=examples --target=alpha --label=rel200
```

When the code is deployed, the label will be suffixed to the version number. So, for example if the version is "alpha3":

`alpha3-rel200`

**Important** Labels must match the following regex: `[a-zA-Z0-9_]+` (one or more alphanumerics or underscores)

## Code Separation, Redirects ##

You can "redirect" from your `deploy.json` file to another, usually intended for situations where your environment configurations are stored in another version control repository.

So, this might be your deploy file from your application folder:

```json
{
    "file":"../vendor/my-company/my-app-environment/deploy.json"
}
```

## Installing this tool ##

In your composer.json require section:

```json
"venditan/appengine-deploy": "1.1.*"
```

or with the command line

```bash
composer require venditan/appengine-deploy:1.1.*
```
