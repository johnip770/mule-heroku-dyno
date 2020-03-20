# Deploy Mule Runtime 4.2.2 to Heroku Dynos

This projects deploys a Mule 4.2.2 Runtime to a Heroku Dyno. Additionally this project automates the registration and de-registration of the Mule Runtime with Anypoint Runtime Manager. 

Additionally the project includes a pre-built Domain project that has an HTTP Listener that leverages the PORT that is assigned when the Dyno is deployed. 

## Requirements

1. Heroku Account
1. Mulesoft Anypoint Platform Account
1. Anypoint Studio

## Setup

```
export app_name="your-chosen-app-name"
```

```
git clone git@github.com:djuang1/mule-heroku-dyno.git && cd mule-heroku-dyno
heroku create $app_name
heroku buildpacks:set https://github.com/heroku/heroku-buildpack-jvm-common.git
heroku buildpacks:add --index 1 https://github.com/heroku/heroku-buildpack-apt
heroku labs:enable runtime-dyno-metadata -a $app_name
```

### Config

Set the following config vars:

```bash
    heroku config:set MULE_ENV="<Anypoint Environment Name>"
    heroku config:set MULE_ORG="<Anypoint Organization Name>"
    heroku config:set MULE_PASSWORD="<Anypoint Password>"
    heroku config:set MULE_USERNAME="<Anypoint Username>"
```

## Deploy Runtime to Heroku
```
    git push heroku master
```

Your first deploy will fail due to memory limitations. You will need to scale up to an instance with more than 1GB of RAM. You can do so with the following command:

```
heroku ps:scale web=1:standard-2x
```

In a seperate terminal, in the same directory:

```
heroku logs -t
```

Once deployed you will see your new runtime in Runtime Manager.

### Custom Mule App Development for your Heroku Mule Runtime

Import the Domain project into your Anypoint Studio:

1. Open Anypoint Studio
1. File -> Import, Select Pacakged mule application (.jar) and hit "Next"
1. Browse to the `Domain-Listener-1.0.0-SNAPSHOT-mule-domain.jar` file located under the domains folder of the project

In your existing project or new project that you plan to deploy to the Heroku Mule Runtime, you need to change the Domain from default to the imported domain project

1. In Anypoint Studio, right click on the root directory of your mule app in Package Explorer and select "Properties"
1. Select "Mule Project" in the left sub-menu and from the "Domain" drop down select `domain-listener-1.0.0-snapshot-mule-domain`, hit Apply and Close

Now your app is configured with the same domain that the Heroku Mule Runtime uses.

When you create a new listener in the app you can select the "Http_Listener_config" option in Basic Settings -> Connector Configuration

### Deploy your .jar to the Heroku based Mule Runtime

1. Log into Anypoint Platform in the browser and navigate to Runtime Manager -> Applications -> Deploy Application
1. Name your application and for "Deployment Target" select your heroku based mule runtime, named $app_name_web.1 (or something similar)
1. Select Choose File and browse to your .jar application file that you exported above
1. Hit "Deploy Application"