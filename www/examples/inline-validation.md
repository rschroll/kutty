---
layout: demo_layout.njk
---
        
## Inline Validation

This example shows how to do inline field validation, in this case of an email address.  To do this
we need to create a form with an input that `POST`s back to the server with the value to be validated
and updates the DOM with the validation results.

We start with this form:

```html
<h3>Signup Form</h3>
<form kt-post="/contact">
  <div kt-target="this" kt-swap="outerHTML">
    <label>Email Address</label>
    <input name="email" kt-post="/contact/email" kt-indicator="#ind">
    <img id="ind" src="/img/bars.svg" class="kutty-indicator"/>
  </div>
  <div class="form-group">
    <label>First Name</label>
    <input type="text" class="form-control" name="firstName">
  </div>
  <div class="form-group">
    <label>Last Name</label>
    <input type="text" class="form-control" name="lastName">
  </div>
  <button class="btn btn-default">Submit</button>
</form>
```
Note that the first div in the form has set itself as the target of the request and specified the `outerHTML`
swap strategy, so it will be replaced entirely by the response.  The input then specifies that it will
`POST` to `/contact/email` for validation, when the `changed` event occurs (this is the default for inputs).
It also specifies an indicator for the request.

When a request occurs, it will return a partial to replace the outer div.  It might look like this:

```html
<div kt-target="this" kt-swap="outerHTML" class="error">
  <label>Email Address</label>
  <input name="email" kt-post="/contact/email" kt-indicator="#ind" value="test@foo.com">
  <img id="ind" src="/img/bars.svg" class="kutty-indicator"/>
  <div class='error-message'>That email is already taken.  Please enter another email.</div>
</div> 
```

Note that this div is annotated with the `error` class and includes an error message element.

This form can be lightly styled with this CSS:

```css
  .error-message {
    color:red;
  }
  .error input {
      box-shadow: 0 0 3px #CC0000;
   }
  .valid input {
      box-shadow: 0 0 3px #36cc00;
   }
```

To give better visual feedback.

Below is a working demo of this example.  The only email that will be accepted is `test@test.com`.

<style>
  .error-message {
    color:red;
  }
  .error input {
      box-shadow: 0 0 3px #CC0000;
   }
  .valid input {
      box-shadow: 0 0 3px #36cc00;
   }
</style>

{% include demo_ui.html.liquid %}

<script>

    //=========================================================================
    // Fake Server Side Code
    //=========================================================================

    // routes
    init("/demo", function(request, params){
      return formTemplate();
    });

    onPost("/contact", function(request, params){
      return formTemplate();
    });
    
    onGet(/\/contact\/email.*/, function(request, params){
        var email = params['email'];
        if(!/\S+@\S+\.\S+/.test(email)) {
          return emailInputTemplate(email, "Please enter a valid email address");
        } else if(email != "test@test.com") {
          return emailInputTemplate(email, "That email is already taken.  Please enter another email.");
        } else {
          return emailInputTemplate(email);
        }
     });
    
    // templates
    function formTemplate(page) {
      return `<h3>Signup Form</h3><form ic-post-to="/contact">
  <div kt-target="this" kt-swap="outerHTML">
    <label>Email Address</label>
    <input name="email" kt-get="/contact/email" kt-indicator="#ind">
    <img id="ind" src="/img/bars.svg" class="kutty-indicator"/>
  </div>
  <div class="form-group">
    <label>First Name</label>
    <input type="text" class="form-control" name="firstName">
  </div>
  <div class="form-group">
    <label>Last Name</label>
    <input type="text" class="form-control" name="lastName">
  </div>
  <button class="btn btn-default">Submit</button>
</form>`;
    }
    
        function emailInputTemplate(val, errorMsg) {
            return `<div kt-target="this" kt-swap="outerHTML" class="${errorMsg ? "error" : "valid"}">
  <label>Email Address</label>
  <input name="email" kt-get="/contact/email" kt-indicator="#ind" value="${val}">
  <img id="ind" src="/img/bars.svg" class="kutty-indicator"/>
  ${errorMsg ? ("<div class='error-message'>" + errorMsg + "</div>") : ""}
</div>`;
        }
</script>
