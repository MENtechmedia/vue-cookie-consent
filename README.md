# Vue Cookie Consent Modal
A Vue module that provides support for multiple cookies to adhere to the EU Cookie Law.

**Note!** This is a package that is very specifically used for Laravel projects that use Vue. We are not responsible or liable for any legal matters involving this package. Using this package is at your own risk.

## Installation

You can install via `npm`.

``` bash
npm i vue-cookie-consent-modal
```

## Usage

#### JavaScript

```js
import CookieConsent from 'vue-cookie-consent-modal';
Vue.component('cookie-consent', CookieConsent);
```

#### Blade

Update the dialog cookie name and/or cookieNames if you want the users to agree to updated cookies.

Set optional to **true** if you want the user to give/retract consent for this cookie. If optional is set to **false**, this cookie will be loaded automatically.

```blade
<cookie-consent dialog-cookie-name="cookie-consent-cookie-dialog-box" cookie-page-url="https://example.com" :cookies="[
    {
      displayName: 'Technical',
      cookieName: 'technical',
      description: 'Technical cookies...',
      optional: false,
      initialStatus: true,
    },
    {
      displayName: 'Functional',
      cookieName: 'functional',
      description: 'Functional cookies...',
      optional: true,
      initialStatus: false,
    },
    {
      displayName: 'Analytical',
      cookieName: 'analytical',
      description: 'Analytical cookies...',
      optional: true,
      initialStatus: false,
    },
    {
      displayName: 'Commercial',
      cookieName: 'commercial',
      description: 'Commercial cookies...',
      optional: true,
      initialStatus: false,
    },
]"></cookie-consent>
```

After closing the cookie dialogue box, the page will refresh. 

```blade
@if (Cookie::get('cookieName_here') == 1)
    {{--Tooling (Facebook, Google, Hotjar, etc)--}}
@endif
```



## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
