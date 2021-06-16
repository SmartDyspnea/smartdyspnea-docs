# SmartDyspnea - IFrame Interagration

We provide a hosted version of our widget that can be added to any HTML website supporting an `iframe`.

## Source site

The site is hosted at: https://widget.smartdyspnea.com

A mock version can be accessed at: https://widget.smartdyspnea.com/mock, this version does not connects to the API and thus does not requires a token. This widget records audio and can be used to test the device permissions, layout and styles, however it does return a random result.


## Integration

The widgets requires a valid `patient_token` ir order to perform a valid test. **Retrieve a `patient_token` for the signed-in user and pass it as query parameter in the iframe url.

```html
<iframe src="https://widget.smartdyspnea.com?token=<patient_token>" allow="camera;microphone"></iframe>
```

You can also test the mock version

```html
<iframe src="https://widget.smartdyspnea.com/mock" allow="camera;microphone"></iframe>
```

**Follow the [backend integration/API](../api/README.md) instructions in order to properly retrieve the `patient_token` using your server-side API. You should not call the API directly from your SPA application and it will expose your `client_credentials` to your end users.