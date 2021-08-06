# SmartDyspnea - IFrame Interagration

We provide a hosted version of our widget that can be added to any HTML website supporting an `iframe`.

## Source site

The site is hosted at: https://widget.smartdyspnea.com
#### Authentication

The widget requires a valid `patient_token` ir order to perform a valid test. **Retrieve a `patient_token` for the signed-in user and pass it as query parameter in the iframe url.

```html
<iframe src="https://widget.smartdyspnea.com?token=<patient_token>" allow="camera;microphone"></iframe>
```

**Follow the [backend integration/API](../api/README.md) instructions in order to properly retrieve the `patient_token` using your server-side API. You should not call the API directly from your SPA application and it will expose your `client_credentials` to your end users.

If the token is missing or is invalid the widget will not render and will show an error message

![](./ss-tokenerror.png)

#### Mocking Authentication and services

If you just wan to test dependencies, aestetics or UX of the component you can bypass authentication by using the mock parameter. However your widget will not call our services and will return random results for the tests.

```html
<iframe src="https://widget.smartdyspnea.com?mock=true" allow="camera;microphone"></iframe>
```
#### Theming the widget

We offer several theming capabilities for the widget:

- `color`: A hex value that will change the primary color of the application
- `font`: A string value containing the name of one of the supported fonts
  - `Roboto`
  - `DM Sans`

Is there something missing you want to change? Talk to us or leave an issue in this repo.

```html
<iframe src="https://widget.smartdyspnea.com?color=#560034&font=DM Sans" allow="camera;microphone"></iframe>
```