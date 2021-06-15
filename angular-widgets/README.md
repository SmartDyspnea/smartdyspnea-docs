# SmartDyspnea - Angular widgets

We provide a native angular implementation of our audio test that can be easily installed from npm and integrated into an existing angular application.

## npm repositories

- [@smart-dyspnea/angular-widgets](https://www.npmjs.com/package/@smart-dyspnea/angular-widgets): Production-ready component, requires a valid `patient_token` to render and be activated.
- [@smart-dyspnea/angular-widgets-mock](https://www.npmjs.com/package/@smart-dyspnea/angular-widgets-mock): Unit test component to check permissions, layout and styles, this does not connects to the API and thus does not requires a token. This widget records audio and can be used to test the device permissions, however it does return a random result.

## Integration instructions

Add the dependency to your `package.json` file using your preferred dependency tool>

```
npm install @smart-dyspnea/angular-widgets
```

```
yarn add @smart-dyspnea/angular-widgets
```

### Dependencies

Install the following peer dependency:

```
npm install recordrtc
```

```
yarn add recordrtc
```

## Usage

### Configuring your application

Import the `AngularWidgetsModule` module into your angular application, like in the following minimal example:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { AngularWidgetsModule } from '@smart-dyspnea/angular-widgets-mock';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AngularWidgetsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

__NOTE:__ If using the __mock widget__ you should import `AngularWidgetsMockModule` instead.

### Calling the widget

**Retrieve a `patient_token` for the signed-in user and pass it as an input to the component.

```html
<smart-dyspnea-breathing-test [token]="{{ tokenValue }}"></smart-dyspnea-breathing-test>
```

In case of integrating the mock component the token gets bypassed so you can pass an arbitrary string.

```html
<smart-dyspnea-breathing-test-mock [token]="token"></smart-dyspnea-breathing-test-mock>
```

**Follow the [backend integration/API](../api/README.md) instructions in order to properly retrieve the `patient_token` using your server-side API. You should not call the API directly from your SPA application and it will expose your `client_credentials` to your end users.