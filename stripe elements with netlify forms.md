# Using Stripe Elements with Netlify Forms

[Stripe Elements](https://stripe.com/elements) are pre-built rich UI components that help you build your own pixel-perfect checkout flows across desktop and mobile.

[Netlify](https://www.netlify.com/) is a unified platform that automates your code to create high-performant, easily maintainable sites and web apps.

Netlify comes with [built-in form handling](https://www.netlify.com/docs/form-handling/). Code an HTML form into any page on your site, add a `netlify` attribute or `data-netlify="true"` to the `<form>` tag, and you can start receiving submissions in your Netlify site admin panel.

When setting up Stripe Elements the example code suggests to dynamically add an input element via JavaScript to be able to transfer the Stripe generated `stripeToken` via the form, as below.

```js
function stripeTokenHandler(token) {
  // Insert the token ID into the form so it gets submitted to the server
  var form = document.getElementById('payment-form');
  var hiddenInput = document.createElement('input');
  hiddenInput.setAttribute('type', 'hidden');
  hiddenInput.setAttribute('name', 'stripeToken');
  hiddenInput.setAttribute('value', token.id);
  form.appendChild(hiddenInput);

  // Submit the form
  form.submit();
}
```

Unfortunately, when testing this I noticed that the `stripeToken` doesn't get passed through to the Netlify site panel.

After some trial and error I suspect this is because the logic Netlify uses to populate a form submission takes all the input elements from the form that are available in the DOM on page load.

To fix this you can add the `stripeToken` input element to the form as a hidden element and then populate the input element once you've received the stripe token.

```html
<form netlify>
	<input type="hidden" name="stripeToken" id="token">
  
	<!-- Your other form elements & input button here -->
</form>
```

Once you've added that into your form element you can amend your JavaScript to place the `stripeToken` into that input.

```js
function stripeTokenHandler(token) {
  // Insert the token ID into the form so it gets submitted to the server
  document.getElementById('token').setAttribute('value', token.id);

  // Submit the form
  form.submit();
}
```

Once you've done that and deployed to Netlify you should see Stripe tokens being passed through to your Netlify site admin panel ready to use in a webhook.