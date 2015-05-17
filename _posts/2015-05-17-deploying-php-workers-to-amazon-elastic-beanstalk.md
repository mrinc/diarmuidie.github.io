---
layout: post
title: 'Deploying PHP Workers to AWS Elastic Beanstalk'
tags:
    - Guide
excerpt: "AWS Elastic Beanstalk is an AWS service that lets you automatically deploy load balanced PHP applications to EC2. One of the lesser know features is the ability to setup EB as a worker to handle long running or computationally intensive tasks (Think image processing/bulk mail sending etc.). EB Workers listen to a SQS queue and automatically run any jobs you put onto the queue. In this post I'll show you how to setup your own EB PHP worker."
---

![Elastic Beanstalk Logo](/media/elastic-beanstalk/elastic-beanstalk.png)

[AWS Elastic Beanstalk](http://aws.amazon.com/elasticbeanstalk/) (EB) is an AWS service that lets you automatically deploy load balanced PHP applications to EC2. One of the lesser know features is the ability to setup EB as a worker to handle long running or computationally intensive tasks (Think image processing/bulk mail sending etc.). EB Workers listen to a SQS queue and automatically run any jobs you put onto the queue. In this post I'll show you how to setup your own EB PHP worker.

## Setting up Elastic Beanstalk Environemnt

The first step is to setup an EB Worker Environment on AWS.

1. Login to your AWS Console and select the [Elastic Beanstalk screen](https://console.aws.amazon.com/elasticbeanstalk/home)

2. Click the "Create New Application" link at the top right of the screen.

3. You will be asked to select between a Web Server Environment and a Worker Environment. Select Worker Environment:

    ![Elastic Beanstalk environment selection](/media/elastic-beanstalk/environment-type.png)

4. Next you will be asked which pre-configured server image you would like to use. I'd recommend using the latest PHP image. You are also given the option of creating a single instance or an auto scaling cluster. When starting out a single instance is easier to configure and cheaper to run. You can always change this setting once your load increases.

    ![Elastic Beanstalk server selection](/media/elastic-beanstalk/server-config.png)

5. At the Application Version screen select the sample application. We will look at how you can upload your own application later.

    ![Elastic Beanstalk application version](/media/elastic-beanstalk/application-version.png)

6. Environment tags are key-value pairs that are made available in your PHP application using the `$_SERVER` superglobal. You can store sensitive data here such as API keys/DB passwords etc. It is also handy to create a Environment variable so that you can run your worker in production or development mode.

    ![Elastic Beanstalk environment tags](/media/elastic-beanstalk/environment-tags.png)

7. The rest of the options are fairly self explanatory and can be safely left at their default values.  When you are ready click "Launch" to create an SQS queue and deploy the sample application worker. You should be aware that from this point on Amazon will charge you for the EC2 instances that are running.

## Writing your PHP Worker Application.

Whenever a job is added the the SQS queue it is sent to localhost port 80 on worker machine (or one of the machines if you have autoscaling setup) as a POST request:

![AWS Elastic Beanstalk Worker Tier Overview](/media/elastic-beanstalk/worker-tier-overview.png)

We will now look at how the PHP application should work.

### Using Composer Packages
If you include a composer.json file in the root of your project EB will automatically run `composer install` when you deploy the project. Nice, right?

### The Basic Index File
Below is a basic worker `index.php` file:

```php
<?php

// Include the composer autoloader
require_once 'vendor/autoload.php';

// Get the post body (This is the content
// of the SQS message)
$postBody = file_get_contents('php://input');

$job = json_decode($postBody);


// Do some work here with the job (resize image etc.)


if ($error) {
    // Returning any non 200 HTTP code will tell the
    // SQS queue that the job failed and to re-queue it.
    http_response_code(500);
}

```

### Testing Your Code Locally
One of the more difficult things with Elastic Beanstalk is how to test your code locally. Firstly you will have to bootstrap the environment variables. Add this to the top of your `index.php` file:

```php

// Check if the mode is set
if (empty($_SERVER['WORKER_MODE'])) {

    // Default to being in Dev mode
    $_SERVER['WORKER_MODE'] = 'dev';

    // Bootstrap any other dev Environment Tags here.
    // Any tags you setup in the EB console when launching
    // the worker should have a dev value here.
    $_SERVER['SOME_API_SECRET_KEY'] = 'my-dev-key';

}

```

Now when you test your code locally (using something like [Postman](https://www.getpostman.com/) to send POST requests to your code) the development Environment Tags will be used, but when you deploy your code to EB it will get the production Environment Tags.

## Deploying the Application
The easiest way to deploy your application is to zip the source and upload it using the AWS EB Console.

A slightly more complicated, but more automated way is to se the [EB command line tool](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html).

***

Now you should have a basic EB worker that can consume jobs from an SQS queue. Once you are more familiar with EB you can investigate some of the more advanced options, like connecting your workers to a RDS instance and configuring auto-scaling to automatically increase the number of workers as the load increases.
