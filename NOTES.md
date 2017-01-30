#  Adding Stripe Payments to your Ruby on Rails Application

For each chapter, any links mentioned or notes will be included below in the appropriate sections.

## 1.1 What we'll be covering

* Stripe - https://stripe.com
* Stripe Subscriptions - https://stripe.com/subscriptions
* Stripe.js - https://stripe.com/docs/custom-form
* Stripe Ruby - https://stripe.com/docs/libraries#ruby-library

## 1.2 Requirements

We're using Ruby 2.3.1 and Rails 5.0.0.1 in this course. If you don't have a recent version of Ruby (2.2+) or Rails (4.2+), you can follow the guides here:

* macOS / OS X - https://gorails.com/setup/osx
* Ubuntu Linux - https://gorails.com/setup/ubuntu
* Windows - http://blog.teamtreehouse.com/installing-rails-5-windows

We're also using Bootstrap v3.3.7 which you can find documentation for at http://getbootstrap.com


## 2.1 Adding products

```
# Generate a new Rails application named store
rails new store

# Create the database model named Product and all the views associated with it
rails g scaffold Product name description:text secret:text

# Remove the default scaffold stylesheet
rm app/assets/styles/scaffolds.scss
```

## 2.2 Creating Users

* Devise - https://github.com/plataformatec/devise

```
# Install the devise gem
bundle

# Install devise into our app
rails g devise:install

# Install the devise views
rails g devise:views

# Create a User model with Devise with Stripe details
rails g devise User stripe_id stripe_subscription_id card_brand card_last4 card_exp_month card_exp_year expires_at:datetime
```

## 2.3 Adding Bootstrap

To grab specific versions of gems, go to [Rubygems.org](https://rubygems.org). It's always good to use specific versions of gems so that major updates don't break your Rails app.

* bootstrap-sass gem - https://github.com/twbs/bootstrap-sass

```
# Install the bootstrap-sass gem after adding it to the Gemfile
bundle install

# Change the extension of our application.css file to support the scss format
mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss
```

Bootstrap example navigation:
**app/views/layouts/application.html.erb**

```
    <nav class="navbar navbar-default">
      <div class="container-fluid">
        <!-- Brand and toggle get grouped for better mobile display -->
        <div class="navbar-header">
          <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
          <a class="navbar-brand" href="#">Store</a>
        </div>

        <!-- Collect the nav links, forms, and other content for toggling -->
        <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
          <ul class="nav navbar-nav">
            <li><%= link_to "Products", products_path %></li>
          </ul>

          <ul class="nav navbar-nav navbar-right">
            <% if user_signed_in? %>
              <li><%= link_to "Account", edit_user_registration_path %></li>
              <li><%= link_to "Log out", destroy_user_session_path, method: :delete %></li>
            <% else %>
              <li><%= link_to "Sign up", new_user_registration_path %></li>
              <li><%= link_to "Login", new_user_session_path %></li>
            <% end %>
          </ul>
        </div><!-- /.navbar-collapse -->
      </div><!-- /.container-fluid -->
    </nav>
```

## 3.1 Signing up for Stripe

You can find both your test and live keys for Stripe here: https://dashboard.stripe.com/account/apikeys

Copy the test keys into your config/secrets.yml file.

**Note:** Use your own keys for this part. The keys you see in the video will no longer work because they have been changed.

## 3.2 Adding a new subscription page

```
# Create the subscriptions views directory
mkdir app/views/subscriptions
```

## 3.3 Creating a card token using Stripe.js

Stripe's Javascript library document can be found here: https://stripe.com/docs/custom-form

**Example payment form**

```
<form action="/your-charge-code" method="POST" id="payment-form">
  <span class="payment-errors"></span>

  <div class="form-row">
    <label>
      <span>Card Number</span>
      <input type="text" size="20" data-stripe="number">
    </label>
  </div>

  <div class="form-row">
    <label>
      <span>Expiration (MM/YY)</span>
      <input type="text" size="2" data-stripe="exp_month">
    </label>
    <span> / </span>
    <input type="text" size="2" data-stripe="exp_year">
  </div>

  <div class="form-row">
    <label>
      <span>CVC</span>
      <input type="text" size="4" data-stripe="cvc">
    </label>
  </div>


  <input type="submit" class="submit" value="Submit Payment">
</form>
```

**Stripe create token Javascript example**

```
$(function() {
  var $form = $('#payment-form');
  $form.submit(function(event) {
    // Disable the submit button to prevent repeated clicks:
    $form.find('.submit').prop('disabled', true);

    // Request a token from Stripe:
    Stripe.card.createToken($form, stripeResponseHandler);

    // Prevent the form from being submitted:
    return false;
  });
});
```

**Stripe token response handler example**

```
function stripeResponseHandler(status, response) {
  // Grab the form:
  var $form = $('#payment-form');

  if (response.error) { // Problem!

    // Show the errors on the form:
    $form.find('.payment-errors').text(response.error.message);
    $form.find('.submit').prop('disabled', false); // Re-enable submission

  } else { // Token was created!

    // Get the token ID:
    var token = response.id;

    // Insert the token ID into the form so it gets submitted to the server:
    $form.append($('<input type="hidden" name="stripeToken">').val(token));

    // Submit the form:
    $form.get(0).submit();
  }
};
```

## 3.4 Sending the card token to the server

**Stripe token response example**

```
{
  id: "tok_u5dg20Gra", // Token identifier
  card: {...}, // Dictionary of the card used to create the token
  created: 1478023847, // Timestamp of when token was created
  currency: "usd", // Currency that the token was created in
  livemode: false, // Whether this token was created with a live API key
  object: "token", // Type of object, always "token"
  used: false // Whether this token has been used
}
```

## 4.1 Installing the Stripe gem

* Stripe gem versions - https://rubygems.org/gems/stripe

## 4.2 Creating plans in Stripe

* Stripe plans page - https://dashboard.stripe.com/test/plans

## 4.3 Creating a customer in Stripe

* Stripe customers page - https://dashboard.stripe.com/test/plans

## 4.4 Subscribing our customer in Stripe

See app/controllers/subscriptions_controller.rb for this episode.

## 4.5 Handling card errors

* Stripe test credit card numbers - https://stripe.com/docs/testing#cards

## 4.6 Protecting products for only paid users

See the code for this episode.

## 5.1 Displaying card on file

See the code for this episode.

## 5.2 Reusing the payment form

See the code for this episode.

## 6.1 Cancelling a subscription

* Cancel a subscription API docs - https://stripe.com/docs/api#cancel_subscription

## 6.2 Resubscribing using a new card

**Manually updating a user's expiration in the rails console**

```
rails c

u = User.last

# Set the user's expiration to a month ago
u.update(expires_at: 1.month.ago)

# Clear the expires_at on a user
u.update(expires_at: nil)
```

## 6.3 Resubscribing using an existing card

See the code for this episode.

## 7.1 Adding the stripe_event gem

* Stripe event types list - https://stripe.com/docs/api#event_types
* Events & webhooks dashboard - https://dashboard.stripe.com/test/events
* `stripe_event` gem - https://github.com/integrallis/stripe_event
* Rubygems page for the `stripe_event` gem - https://rubygems.org/gems/stripe_event

## 7.2 Listening to the charge.succeeded event

```
# Create the webhooks folder
mkdir app/models/webhooks
```

## 7.3 Creating a charge model

**Create a Charge model to log payments**

```
rails g model Charge user:references stripe_id amount:integer card_brand card_last4 card_exp_month card_exp_year
```

**Test the webhook handler class**

```
rails console

e = Stripe::Event.retrieve("YOUR_CHARGE_SUCCEEDED_EVENT_ID")
Webhooks::ChargeSucceeded.new.call(e)
#=> #<User>
```

## 7.4 Displaying Charges in the account page

* Bootstrap tables - http://getbootstrap.com/css/#tables

## 7.5 Making PDF Receipts

* Receipts gem - https://github.com/excid3/receipts
* Rubygems page for the Receipts gem - https://rubygems.org/gems/receipts
