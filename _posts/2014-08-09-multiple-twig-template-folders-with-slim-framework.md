---
layout: post
title: 'Multiple Twig Template Folders with Slim Framework'
redirect_from: /blog/post/multiple-twig-template-folders-with-slim-framework/
tags:
    - Guide
    - php

---
This post is more for my own records than anything else as I couldn't find this information anywhere on the internet.

I have a [Slim Framework](http://slimframework.com/) application that uses [Twig](http://twig.sensiolabs.org/) for it's templates. I wanted to initiate the Twig instance in my bootstrap file, but then to dynamically update the template directory within my class files depending on the request. It took me a while to figure out how to set Slim to look in multiple directories, or to change the template directory altogether, to render a view.

This method also lets you change the template directory after the Twig instance has been initiated.

First step is to make sure you have Slim, Slim-views and Twig installed through your `composer.json` file:

{% highlight JSON %}
{
    "require": {
        "slim/slim": "2.*",
        "slim/views": "0.1.*",
        "twig/twig": "1.*"
    }
}
{% endhighlight %}

In your bootstrap file load your Twig views as normal:

{% highlight ruby %}
<?php
require 'vendor/autoload.php';

$app = new \Slim\Slim(array(
    'templates.path' => './templates'
    'view' => new \Slim\Views\Twig()
));
{% endhighlight %}

With this setup Twig will only look in the `template` folder for templates. My first attempt to change this was using Slim's `setTemplatesDirectory()` function. However this function only changes the Twig template if it is called before Twig is initialised; not what I wanted.

{% highlight ruby %}
// After instantiation
$view = $app->view();
$view->setTemplatesDirectory('./templates');

// Twig template director only changes if
// this is called before "new \Slim\Views\Twig()"
{% endhighlight %}

To change the template directory we will have to interact directly with the Twig instance:

{% highlight ruby %}
// Get an instance of the Twig Environment
$twig = $app->view->getInstance();

// From that get the Twig Loader instance (file loader in this case)
$loader = $twig->getLoader();

// You can now set a new path (or array of paths)...
$loader->setPaths('newTemplateDir');

// Or add an additional path to the existing
$loader->addPath('newTemplateDir');
{% endhighlight %}

I hope someone else finds this useful.
