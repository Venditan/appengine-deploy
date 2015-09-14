# App Engine Deploy #

Environment and Deployment Manager for *Google App Engine* Applications

Working towards this: http://12factor.net/config

## Examples ##

Deploy the **examples** module to the **alpha** target environment

```bash
vendor/bin/deploy examples:alpha
```

Create a template **deploy.json** file

```bash
vendor/bin/deploy init
```

## Usage ##

Deployment targets and environments are configured in `deploy.json` and executed using the `deploy` command, which is made available the `vendor/bin` folder by Composer.

For example, from your project root:

```bash
vendor/bin/deploy run default:live
```

```bash
deploy [verbose] init
```

```bash
deploy [verbose] targets
```

```bash
deploy [verbose] test module:target
```

```bash
deploy [verbose] run module:target
```

### Default Module ###

You can use either `app` or `default` to deploy the default App Engine module (which is configured in your `app.yaml` file).

## deploy.json ##

You should create `deploy.json` in your project root (you can use the `deploy init` command to create a template file).

Here is an example file:

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

### Targets ###

Each target is a different deployment, like "staging" and "production".

They must be uniquely named.

### Versions ###

If you suffix your version name with `++` then we will auto-increment the version on each deployment. 

In the example above, the first deployment gets `alpha1` and the second `alpha2` and so on.

In order to do this, we have to be able to detect what versions are already running. So, if you delete all your versions, we will start at 1 again.

### Code Separation, Redirects ###

You can "redirect" from your `deploy.json` file to another, usually intended for situations where your environment configurations are stored in another version control repository.

So, this might be your deploy file from your application folder:

```json
{
    "file":"../vendor/my-company/my-app-environment/deploy.json"
}
```

## Installing this library ##

In your composer.json require section:

```json
"venditan/appengine-deploy": "dev-master"
```

