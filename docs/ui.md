---
sidebar_position: 10
---

# UI

This is the frontend for the Xpanse project which allows cloud service providers to register managed services to the
service catalog and also for end users to deploy services from the service catalog and manage them.

## Development Setup

Project is built using `ReactJS` library. As we use `TypeScript` here, we must ensure all objects have its type explicit
defined.

GUI components are built using `antd` library.

Authentication and authorization are built using `Zitadel` which is an oauth2 provider.

## Zitadel Configuration

Before we can start using the UI, we must ensure `Zitadel` instance that we consider using,
has all the required configuration. Details can be found [here](https://github.com/eclipse-xpanse/xpanse-iam/blob/main/zitadel/terraform/README.md).

## Configuration Properties

All required configuration parameters must be added to .env
file [here](https://github.com/eclipse-xpanse/xpanse-ui/blob/main/.env).
Even if we do not have a valid default value, we must just add empty value.
This file serves as a reference to all required properties.

> **Very Important**: Since React is compiled to a static app, all configuration values can be seen directly in the
> browser too.
> Therefore, no secure configurations such as passwords, keys, etc. must be added here.

### Setting Configuration Values

#### .env Files

1. Set values in the .env files.
   All default values are set in .env files.
   These are automatically loaded by `React` and there is no need to do anything for this to be loaded.
2. For non-default properties or to override the values is .env, we can set the values in new .env files and load them
   using `env-cmd` framework which will automatically inject the variables.
   Example can be
   found [here](https://github.com/eclipse-xpanse/xpanse-ui/blob/main/package.json#L20)

> **Important**: .env files must be used only for default configurations or for dev configurations values.

#### Environment Variables

All variables can be overridden by setting environment variables and then running the `npm run start` for development or
with `docker run` for production.

### Getting Configuration Values

We have a custom implementation which reads the configuration from all sources and provides a unified configuration map.
Only this must be used for reading configuration from the React app.

Implementation can be found [here](https://github.com/eclipse-xpanse/xpanse-ui/blob/main/src/config/config.ts#L16).

## Starting local development server

In the project directory, you can run the below command to start the local development server. This also additionally
needs `nodejs` to be installed on the development machine.

If there is a local development Zitadel instance and backend API instance, then we must
set `REACT_APP_ZITADEL_CLIENT_ID` and `REACT_APP_XPANSE_API_URL` environment variables with respective values
and then run the below command for the application to start.

```shell
$ npm run start
```

If you wish to use our central Zitadel testbed instance, then simply start the application with the below command.

```shell
$ npm run start-with-zitadel-testbed
```

Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

### Static Code Analysis

We use `eslint` and `knip` to statically analyze code.
Always run the below command locally to ensure the changes made results in no errors. This will also validate the code
format.
In case of any errors, the CI pipeline will fail when a PR is opened.

```shell
 npx eslint . --ext .js,.jsx,.ts,.tsx --config package.json --max-warnings=0
 npx knip
```

### Code Formatting

We use `prettier` to format our UI code. To auto format the code, you can run the below command.

```shell
npx prettier --config .prettierrc --write .
```

### Generate Rest Client for Xpanse API

We use the openapi generator to generate data models and rest client from the openapi json file.
The following steps must be followed to generate a new client and data models whenever there is a new version if the
swagger json.

1. Copy the openapi file
   to [OpenApi JSON File](https://github.com/eclipse-xpanse/xpanse-ui/blob/main/src/xpanse-api/api.json)
2. Run the code generator as below
    ```shell
        cd src/xpanse-api
        npx openapi-typescript-codegen --input api.json --output ./generated --exportSchemas false
    ```
    This step will generate all required models and client
3. Add license headers.
4. Format newly generated files.

## Build for production

The only recommended way to run UI in production is to use the docker image

## Docker Image

Docker image for the UI project is based on nginx base image. This is because the project only serves static content.

As part of our UI release process, we deliver our official images to GitHub packages.
All available images can be found [here](https://github.com/orgs/eclipse-xpanse/packages?tab=packages&q=xpanse-ui).

### Run UI Container

Container runs the application on port `3000` by default and exposes the UI using HTTP.
We must use another SSL load balancer to expose the UI for end users.

Production configuration values must be passed as environment variables to docker run for the below vars using `-e` option.

```dotenv
REACT_APP_ZITADEL_AUTHORITY_NAME # URL for the Oauth provider
REACT_APP_ZITADEL_CLIENT_ID # Client ID provided by the Oauth provider for UI
REACT_APP_XPANSE_API_URL # Backend API URL
```

```shell
docker pull ghcr.io/eclipse-xpanse/xpanse-ui:${version}
docker run -d --name ui xpanse-ui
```

### Application Logs

All logs from nginx are routed to stdout by default. Using the below command, all application logs can be viewed.

```shell
docker logs ui
```
