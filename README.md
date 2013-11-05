#About Paymentwall
[Paymentwall](http://paymentwall.com/?source=gh) is the leading digital payments platform for globally monetizing digital goods and services. Paymentwall assists game publishers, dating sites, rewards sites, SaaS companies and many other verticals to monetize their digital content and services. 
Merchants can plugin Paymentwall's API to accept payments from over 100 different methods including credit cards, debit cards, bank transfers, SMS/Mobile payments, prepaid cards, eWallets, landline payments and others. 

To sign up for a Paymentwall Merchant Account, [click here](http://paymentwall.com/signup/merchant?source=gh).

#Paymentwall PHP Library
This library allows developers to use [Paymentwall APIs](http://paymentwall.com/en/documentation/API-Documentation/722?source=gh) (Virtual Currency, Digital Goods featuring recurring billing, and Virtual Cart).

To use Paymentwall, all you need to do is to sign up for a Paymentwall Merchant Account so you can setup an Application designed for your site.
To open your merchant account and set up an application, you can [sign up here](http://paymentwall.com/signup/merchant?source=gh).

#Installation
To install the library in your environment, you can download the [ZIP archive](https://github.com/paymentwall/paymentwall-php/archive/master.zip), unzip it and place into your project.

Alternatively, you can run:

  <code>git clone git://github.com/paymentwall/paymentwall-php.git</code>

Then use a code sample below.

#Code Samples

##Digital Goods API

####Initializing Paymentwall
<pre><code>require_once('/path/to/paymentwall-php/libs/paymentwall.php');
Paymentwall_Base::setApiType(Paymentwall_Base::API_GOODS);
Paymentwall_Base::setAppKey('YOUR_APPLICATION_KEY'); // available in your Paymentwall merchant area
Paymentwall_Base::setSecretKey('YOUR_SECRET_KEY'); // available in your Paymentwall merchant area
</code></pre>

####Widget Call
[Web API details](http://www.paymentwall.com/en/documentation/Digital-Goods-API/710#paymentwall_widget_call_flexible_widget_call)

The widget is a payment page hosted by Paymentwall that embeds the entire payment flow: selecting the payment method, completing the billing details, and providing customer support via the Help section. You can redirect the users to this page or embed it via iframe. Below is an example that renders an iframe with Paymentwall Widget.

<pre><code>$widget = new Paymentwall_Widget(
  'user40012',                  // id of the end-user who's making the payment
  'p1_1',                   // widget code, e.g. p1; can be picked inside of your merchant account
  array(
    new Paymentwall_Product(
      'product301',                             // id of the product in your system
      9.99,                                     // price
      'USD',                                    // currency code
      'Gold Membership',                        // product name
      Paymentwall_Product::TYPE_SUBSCRIPTION,   // this is a time-based product
      1,                                        // duration is 1
      Paymentwall_Product::PERIOD_TYPE_MONTH,   //               month
      true                                      // recurring
    )
  ),
  array('email' => 'user@hostname.com')     // additional parameters
);
echo $widget->getHtmlCode();
</pre></code>

<h4>Pingback Processing</h4>

The Pingback is a webhook notifying about a payment being made. Pingbacks are sent via HTTP/HTTPS to your servers. To process pingbacks use the following code:
<pre><code>$pingback = new Paymentwall_Pingback($\_GET, $\_SERVER['REMOTE_ADDR']);
if ($pingback->validate()) {
  $productId = $pingback->getProduct()->getId();
  if ($pingback->isDeliverable()) {
  // deliver the product
  } else if ($pingback->isCancelable()) {
  // withdraw the product
  } 
  echo 'OK'; // Paymentwall expects response to be OK, otherwise the pingback will be resent
} else {
  echo $pingback->getErrorSummary();
}</pre></code>

<h2>Virtual Currency API</h2>

<h4>Initializing Paymentwall</h4>
<pre><code>require_once('/path/to/paymentwall-php/libs/paymentwall.php');
Paymentwall_Base::setApiType(Paymentwall_Base::API_VC);
Paymentwall_Base::setAppKey('YOUR_SECRET_KEY'); // available in your Paymentwall merchant area
Paymentwall_Base::setSecretKey('YOUR_SECRET_KEY'); // available in your Paymentwall merchant area
</code></pre>

####Widget Call
<pre><code>$widget = new Paymentwall_Widget(
  'username', // id of the end-user who's making the payment
  'p10_1', // widget code, e.g. p1; can be picked inside of your merchant account
  array(), // array of products - leave blank for Virtual Currency API
  array('email' => 'user@hostname.com') // additional parameters
);
echo $widget->getHtmlCode();
// Now you can embed the iframe with Paymentwall widget into your website
// &lt;iframe src=&quot;https://api.paymentwall.com/api/ps?key=0b1552192f37f9dd84150a39be14a5e9&amp;uid=yeexel&amp;widget=p10_1&amp;sign_version=1&amp;sign=7c0be7b97bc93de6074eed243c65aa77&quot;  frameborder=&quot;0&quot; width=&quot;750&quot; height=&quot;800&quot;&gt;&lt;/iframe&gt;</code></pre>

<h4>Pingback Processing</h4>

<pre><code>$pingback = new Paymentwall_Pingback($\_GET, $\_SERVER['REMOTE_ADDR']);
if ($pingback->validate()) {
  $virtualCurrency = $pingback->getVirtualCurrencyAmount();
  if ($pingback->isDeliverable()) {
  // deliver the virtual currency
  } else if ($pingback->isCancelable()) {
  // withdraw the virual currency
  } 
  echo 'OK'; // Paymentwall expects response to be OK, otherwise the pingback will be resent
} else {
  echo $pingback->getErrorSummary();
}</pre></code>

<h2>Cart API</h2>

<h4>Initializing Paymentwall</h4>
<pre><code>require_once('/path/to/paymentwall-php/libs/paymentwall.php');
Paymentwall_Base::setApiType(Paymentwall_Base::API_CART);
Paymentwall_Base::setAppKey('YOUR_APPLICATION_KEY'); // available in your Paymentwall merchant area
Paymentwall_Base::setSecretKey('YOUR_SECRET_KEY'); // available in your Paymentwall merchant area
</code></pre>

####Widget Call
<pre><code>$widget = new Paymentwall_Widget(
  'username', // id of the end-user who's making the payment
  'p1_1', // widget code, e.g. p1; can be picked inside of your merchant account,
  array(
    new Paymentwall_Product('1', 3.33, 'EUR'), // first product in cart
    new Paymentwall_Product('2', 7.77, 'EUR')  // second product in cart
  ),
  array('email' => 'user@hostname.com') // additional params
);
echo $widget->getHtmlCode();</code></pre>

<h4>Pingback Processing</h4>

<pre><code>$pingback = new Paymentwall_Pingback($\_GET, $\_SERVER['REMOTE_ADDR']);
if ($pingback->validate()) {
  $products = $pingback->getProducts();
  if ($pingback->isDeliverable()) {
  // deliver products from the cart
  } else if ($pingback->isCancelable()) {
  // withdraw products from the cart
  } 
  echo 'OK'; // Paymentwall expects response to be OK, otherwise the pingback will be resent
} else {
  echo $pingback->getErrorSummary();
}</pre></code>
