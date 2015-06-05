---
layout: default
group: config-guide
subgroup: Bootstrap
title: MAGE_RUN_TYPE, MAGE_RUN_CODE
menu_title: MAGE_RUN_TYPE, MAGE_RUN_CODE
menu_order: 500
menu_node: 
github_link: config-guide/bootstrap/mage-run.md
---

#### Contents
*	<a href="#magerun-introduction">Introduction to multiple Magento stores and websites</a>
*	<a href="#magerun-conf">Configure Magento</a>
*	<a href="#magerun-set">Set values for MAGE_RUN_TYPE and MAGE_RUN_CODE</a>


<h2 id="magerun-introduction">Introduction to multiple Magento stores and websites</h2>
Use the `MAGE_RUN_TYPE` and `MAGE_RUN_CODE` variables when you have multiple stores and websites using the same Magento installation. This allows multiple storefronts to share a common code base and Magento Admin, simplifying administration. 

In addition to sharing a codebase, stores can share customers, product catalogs, and configuration settings based on how you choose to configure your websites.

A typical use of `MAGE_RUN_TYPE` and `MAGE_RUN_CODE` is to set up stores with different options in different domains. For example, you could have one set of categories and products on one domain and the other one will be on different language.

<div class="bs-callout bs-callout-info" id="info">
<span class="glyphicon-class">
  <p>This topic discusses how to set the variables only. Details about deploying the Magento software in multiple domains is beyond the scope of this topic.</p></span>
</div>

<h2 id="magerun-conf">Configure Magento websites and stores</h2>
This section discusses the minimum tasks required to use `MAGE_RUN_TYPE` and `MAGE_RUN_CODE`. For more details about websites and stores, see the Magento 2 Users Guide when it is available.

We use the following terms:

*	*Website* is the top-level container for sites, shipping methods, payment methods, and so on. To create completely separate sites that do not share cart, shipping methods, and so on,  create separate websites. 

*	*Store* is contained by a website. In turn, a store contains at least one *store view*. 

	Multiple Stores can share cart, user sessions, payment gateways, and so on, but they have separate catalog structures. 

	Store views change the way pages are presented, and are typically used to display a site different layouts or languages. 

Each website and each store view must have a unique identifer. This identifier is required to use `MAGE_RUN_TYPE` and `MAGE_RUN_CODE`.

To create websites and stores:

1.	Log in to the Magento Admin as a user authorized to create websites, stores, and store views.
2.	Click **Stores** > **All Stores**.
3.	To create a website, click **Create Website**.
4.	To create a store, click **Create Store**.
5.	To create a store view, click **Create Store View**.
5.	When you create your website or store view, make note of its unique identifier.

<!-- https://www.properhost.com/support/kb/30/How-To-Setup-Magento-With-Multiple-Stores-And-Domains -->
<!-- http://inchoo.net/magento/how-to-set-multiple-stores-websites-with-one-magento-installation-on-different-domains/ -->


<h2 id="magerun-set">Set values for MAGE_RUN_TYPE and MAGE_RUN_CODE</h2>
We recommend you set values for `MAGE_RUN_TYPE` and `MAGE_RUN_CODE` in an entry point script, rather than as system or web server environment variables. This is more flexible and makes more sense because you can use store-specific values in entry points.

If necessary, create an `index.php` entry point script for your website or store view and add to it the following:

{% highlight php %}
<?php
 $params = $_SERVER;
 $params[\Magento\Store\Model\StoreManager::MAGE_RUN_CODE] = '<code>';
 $params[\Magento\Store\Model\StoreManager::MAGE_RUN_TYPE] = '{store|website}';
 $bootstrap = \Magento\Framework\App\Bootstrap::create(BP, $params);
 $app = $bootstrap->createApplication('Magento\Framework\App\Http');
 $bootstrap->run($app);
 ?>
 {% endhighlight %}

Sample `index.php` that starts Magento with a website with the code `frenchsite.example.com`:

{% highlight php %}
<?php
/**
 * Application entry point
 *
 * Copyright © 2015 Magento. All rights reserved.
 * See COPYING.txt for license details.
 */

try {
    require __DIR__ . '/app/bootstrap.php';
} catch (\Exception $e) {
    echo <<<HTML
<div style="font:12px/1.35em arial, helvetica, sans-serif;">
    <div style="margin:0 0 25px 0; border-bottom:1px solid #ccc;">
        <h3 style="margin:0;font-size:1.7em;font-weight:normal;text-transform:none;text-align:left;color:#2f2f2f;">
        Autoload error</h3>
    </div>
    <p>{$e->getMessage()}</p>
</div>
HTML;
    exit(1);
}

 $params = $_SERVER;
 $params[\Magento\Store\Model\StoreManager::MAGE_RUN_CODE] = 'frenchsite.example.com';
 $params[\Magento\Store\Model\StoreManager::MAGE_RUN_TYPE] = 'website';
 $bootstrap = \Magento\Framework\App\Bootstrap::create(BP, $params);
 $app = $bootstrap->createApplication('Magento\Framework\App\Http');
 $bootstrap->run($app);
 ?>

{% endhighlight %}
