# react-modal-login

Login modal component built with React. Besides a traditional sign in and sign up forms you may use
our pre-configured social login buttons. In the current version we offer a support for Facebook and Google.

Since we intend to target the module for developers, we decided to offer bigger customization options.
This requires some functions to be created in a parent component. Don't worry though. We will cover that topic further
in this manual.



### Installation

```bash
npm install --save react-modal-login
```


### Demos

You may find some samples of the plugin at [developers.thebeaverhead.com/react-modal-login](http://developers.thebeaverhead.com/react-modal-login)


###Social support

If you're willing to use social login buttons, you need to configure them first.
You may either keep those settings in a separate file or no, it's up to you.
For the sake of keeping everything in order, we demonstrate how to use it in
**social-config.js** file

**Typical Facebook configuration**
```js
const facebook = {
  id: 'YOUR FB APP ID GOES HERE',
  cookie: true,
  xfbml: true,
  version: 'v2.10'
};

export const facebookConfig = facebook;
```

**Typical Google configuration**
```js
const google = {
  id: 'YOUR GOOGLE APP ID GOES HERE',
  scope: "https://www.googleapis.com/auth/plus.login"
};

export const googleConfig = google;
```



###Managing state in parent component

Just as I wrote at the upper part of this manual, our component is highly customizable. Thus, some of the methods
needed for basic functionality need to be passed down from the parent component. Just like in the example below.
If you would like to enjoy a full range of plugin's functionality you need to:

* `declare initial state` - it is recommended to do this in a constructor function
```js
  constructor(props) {
    super(props);

    this.state = {
      showModal: false,
      loading: false,
      error: null
    };

  }
```
You need to create a methods to be passed to ReactModalLogin component such as:

* `openModal()` - action to open the modal. For instance, you may bind this to click of the "sign in" button etc.
* `closeModal()` - action to close the modal. You need to pass it to the component later on to enable hiding the modal
    by clicking close button or clicking in overlay wrap
* `startLoading()` - action needed to serve the loading event. When there is an asynchronous action in component you may
    be willing to make other elements inactive
* `finishLoading()` - that action is indicating the end of loading and it's making all the elements inside the component
    active again
* `onTabsChange()` - callback to clicking tab button. As provided in our example, you may use it to clean the error state

Most Likely you will be in the need of using social login callback actions. Those may execute some code in your app as well
as display the fail error:

* `onLoginSuccess()` - success login callback
* `onLoginFail()` - "login failed" callback. It is recommended to execute setState() function here which changes the error
    state


### Example

```js
import React from 'react';
import ReactModalLogin from 'react-modal-login';

import {facebookConfig, googleConfig} from "social-config";

class Sample extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      showModal: false,
      loading: false,
      error: null
    };

  }

  openModal() {
    this.setState({
      showModal: true,
    });
  }

  closeModal() {
    this.setState({
      showModal: false,
      error: null
    });
  }
  
  onLoginSuccess(method, response) {
    console.log('logged successfully with ' + method);
  }

  onLoginFail(method, response) {
    console.log('logging failed with ' + method);
    this.setState({
      error: response
    })
  }

  startLoading() {
    this.setState({
      loading: true
    })
  }

  finishLoading() {
    this.setState({
      loading: false
    })
  }

  onTabsChange() {
    this.setState({
      error: null
    });
  }


  render() {

    return (
      <div>

        <button
          onClick={() => this.openModal()}
        >
          Open Modal
        </button>

        <ReactModalLogin
          visible={this.state.showModal}
          onCloseModal={this.closeModal.bind(this)}
          loading={this.state.loading}
          error={this.state.error}
          tabs={{
            onChange: this.onTabsChange.bind(this)
          }}
          loginError={{
            label: "Couldn't sign in, please try again."
          }}
          registerError={{
            label: "Couldn't sign up, please try again."
          }}
          startLoading={this.startLoading.bind(this)}
          finishLoading={this.finishLoading.bind(this)}
          providers={{
            facebook: {
              config: facebookConfig,
              onLoginSuccess: this.onLoginSuccess.bind(this),
              onLoginFail: this.onLoginFail.bind(this),
              label: "Continue with Facebook"
            },
            google: {
              config: googleConfig,
              onLoginSuccess: this.onLoginSuccess.bind(this),
              onLoginFail: this.onLoginFail.bind(this),
              label: "Continue with Google"
            }
          }}
        />
      </div>
    )
  }
}
```
### Component's properties

* `mainWrapClass` |_string_| - class of the whole component's wrapper (which contains both overlay and the popup itself)

* `visible` |_boolean_| - boolean which determines whether popups should be visible or no

* `onCloseModal` |_function_| - function closing the modal

* `onBeforeCloseModal` |_function_| - action executing just before the closing of modal

* `onAfterCloseModal` |_function_| - action executing just after the closing of modal

* `overlayClass` |_string_| - class of the popup wrap overlay

* `loginError` |_object_| - login error message object
    * `containerClass` |_string_| - login error container class
    * `label` |_string_ or _element_| - text of failed login message
    
* `registerError` |_object_| - register error message object
    * `containerClass` |_string_| - register error container class
    * `label` |_string_ or _element_| - text of failed register message
    
* `loader` |_object_| - loader svg object
    * `containerClass` |_string_| - loader container class
    * `disabled` |_boolean_| - boolean determining if the loader should be disabled
    
* `separator` |_object_| - object of separator which sits between social login buttons and custom form
    * `containerClass` |_string_| - separator class
    * `label` |_string_ or _element_| - text of separator
    
* `closeBtn` |_object_| - close button object
    * `containerClass` |_string_| - close button container class
    
* `tabs` |_object_| - sign in / sign up tabs object
    * `containerClass` |_string_| - tabs container class
    * `onChange` |_function_| - callback which fires after the change of a tab
    * `loginLabel` |_string_| - text of login label
    * `registerLabel` |_string_| - text of register label
    
* `additionalWrap` |_object_| - that's the div which shows loader and error messages in case we don't include our custom form
    * `containerClass` |_string_| - additionalWrap container class
    * `disabled` |_boolean_| - boolean determining if the additionalWrap should be disabled
    
* `providers` |_object_| - object containing social buttons providers data
    * `facebook` - |_object_| - facebook button object
        * `btnClass` |_string_| - button custom class
        * `config` |_object_| - Facebook API config parameters used to init the modal
            (for more info please visit [Facebook developers page](https://developers.facebook.com/docs/facebook-login/review))
        * `btn` |_element_| - if you would like to insert custom button for facebook login include it here
        * `onLoginSuccess` |_function_| - login success callback
        * `onLoginFail` |_function_| - login fail callback
        * `label` |_string_ or _element_| - text inside FB button
        
    * `google` - |_object_| - google button object
        * `btnClass` |_string_| - button custom class
        * `config` |_object_| - Google API config parameters used to init the modal
            (for more info please visit [Google developers page](https://developers.google.com/identity/sign-in/web/build-button))
        * `btn` |_element_| - if you would like to insert custom button for google login include it here
        * `onLoginSuccess` |_function_| - login success callback
        * `onLoginFail` |_function_| - login fail callback
        * `label` |_string_ or _element_| - text inside Google button
    
* `form` |_object_| - object of custom login/register form you may include in popup
    * `onLogin` |_function_| - function executing when user click 'sign in' button
    * `onRegister` |_function_| - function executing when user click 'sign up' button
    * `loginContainerClass` |_string_| - class of login form container
    * `registerContainerClass` |_string_| - class of register form container
    
    * `loginBtn` - |_object_| - login button
        * `buttonClass` |_string_| - class of login button
        * `label` |_string_ or _element_| - text inside login button
        
    * `registerBtn` - |_object_| - register button
        * `buttonClass` |_string_| - class of login button
        * `label` |_string_ or _element_| - text inside login button
        
    * `loginInputs` |_array_| - Array of objects. Every each of them represents single login input field 
        * `containerClass` |_string_| - class of input wrap
        * `type` |_string_| - HTML type of input (email, password, text, number etc.)
        * `inputClass` |_string_| - class of the input
        * `id` |_string_| - input's id
        * `name` |_string_| - input's name
        * `placeholder` |_string_| - input's placeholder
        * `label` |_string_ or _element_| - label of the input
        
    * `registerInputs` |_array_| - Array of objects. Every each of them represents single register input field
        * `containerClass` |_string_| - class of input wrap
        * `type` |_string_| - HTML type of input (email, password, text, number etc.)
        * `inputClass` |_string_| - class of the input
        * `id` |_string_| - input's id
        * `name` |_string_| - input's name
        * `placeholder` |_string_| - input's placeholder
        * `label` |_string_ or _element_| - label of the input
    

### Development
Want to run demos locally

```bash
git clone https://github.com/akiran/react-slick
npm install
npm start
open http://localhost:8080
```