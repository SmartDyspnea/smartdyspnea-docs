# SmartDyspnea - IFrame Interagration

We provide a hosted version of our widget that can be added to any HTML website supporting an `iframe`.

## Source site

The site is hosted at: https://widget.smartdyspnea.com

## Configuration
### Authentication

The widget requires a valid `patient_token` ir order to perform a valid test. **Retrieve a `patient_token` for the signed-in user and pass it as query parameter in the iframe url.

```html
<iframe src="https://widget.smartdyspnea.com?token=<patient_token>" allow="camera;microphone"></iframe>
```

**Follow the [backend integration/API](../api/README.md) instructions in order to properly retrieve the `patient_token` using your server-side API. You should not call the API directly from your SPA application and it will expose your `client_credentials` to your end users.

If the token is missing or is invalid the widget will not render and will show an error message

![](./ss-tokenerror.png)

### Mocking Authentication and services

If you just wan to test dependencies, aestetics or UX of the component you can bypass authentication by using the mock parameter. However your widget will not call our services and will return random results for the tests.

```html
<iframe src="https://widget.smartdyspnea.com?mock=true" allow="camera;microphone"></iframe>
```

### Controlling views and navigation

The widget has three main screens:

- Instructions: Provides instructions on how too perform the test is just informative
- Record: This UI records the audio and sends it to our servers
- Results: This UI shows the information about the last performed test

You can chose which screens to show to the user depending on internal status, to this extent, the widget is multipurpose and can be used to post and review a test or just to review the latest test.

To control the sequence you can use the `step` parameter like this:

- Complete sequence

```html
<iframe src="https://widget.smartdyspnea.com" allow="camera;microphone"></iframe>
```

- Skip instructions

```html
<iframe src="https://widget.smartdyspnea.com?step=RECORDING" allow="camera;microphone"></iframe>
```

- Just show results

```html
<iframe src="https://widget.smartdyspnea.com?step=RESULTS" allow="camera;microphone"></iframe>
```

### Theming the widget

We offer several theming capabilities for the widget:

- `color`: A hex value that will change the primary color of the application
- `font`: A string value containing the name of one of the supported fonts
  - `Roboto`
  - `DM Sans`

Is there something missing you want to change? Talk to us or leave an issue in this repo.

```html
<iframe src="https://widget.smartdyspnea.com?color=#560034&font=DM Sans" allow="camera;microphone"></iframe>
```