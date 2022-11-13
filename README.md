# Vue3 Router Meta Tags

Automatically handle meta tags in your Vue3 Router.

## Table of Contents

- [Vue3 Router Meta Tags](#vue3-router-meta-tags)
  - [Table of Contents](#table-of-contents)
  - [Installation](#installation)
  - [Usage](#usage)
    - [Configuration](#configuration)
      - [defaultName](#defaultname)
      - [defaultLocale](#defaultlocale)
      - [locales](#locales)
      - [preconnect](#preconnect)
      - [textCallback](#textcallback)
    - [Manually setting meta tags](#manually-setting-meta-tags)
    - [Updating the current locale](#updating-the-current-locale)
    - [Setting your own JSON schema](#setting-your-own-json-schema)

## Installation

```bash
npm install vue3-router-meta-tags
```

## Usage

First, import the plugin and install it in your Vue app:

```js
// main.js
import { metaTagPlugin } from "@m-media/vue3-meta-tags";

app.use(
  metaTagPlugin,
  {
    /* optional configuration here */
  },
  router
);
```

The third parameter is the router instance. The second parameter is an optional configuration object.

The package will automatically set some defaults on every page, but you can set your own meta attributes in a route too:

```js
{
      path: "/",
      name: "home",
      meta: {
        title: "Home",
        description: "This is the home page",
        image: "https://example.com/image.png",
        locale: "en_US",
        metaTags:
          {
            "og:title": "Home",
            ... // other meta tags
          },
      },
      component: Home,
},
```

### Configuration

The full configuration object that can be passed looks like this:

```js
app.use(
  metaTagPlugin,
  {
    defaultName: import.meta.env.VITE_APP_NAME,
    defaultLocale: i18n.global.locale.value,
    locales: SUPPORT_LOCALES,
    preconnect: [import.meta.env.VITE_API_URL, "https://js.stripe.com"],
    textCallback: (text: string) => {
      return i18n.global.t(text);
    },
  },
  router
);
```

#### defaultName

The default name of your app. This will be used if no name/title is specified in the meta tags, and also for some tags like `og:site_name`.

#### defaultLocale

The default locale of your app. This sets the `lang` attribute of the `html` tag, as well as some other tags like `og:locale`. If the set locale is a right-to-left language, the `dir` attribute of the `html` tag will be set to `rtl`, otherwise its set to `ltr`.

#### locales

An array of locales that your app supports. This is used to generate the `hreflang` meta tags.

#### preconnect

An array of URLs that you want to preconnect to. This is used to generate the `link` tags with `rel="preconnect"`.

#### textCallback

A callback function that is used to translate the text in the meta tags. This is useful if you are using a library like Vue I18n.

### Manually setting meta tags

You can run

```js
import { setMetaAttributes } from "@m-media/vue3-meta-tags";

const to = router.currentRoute.value;
const from = router.currentRoute.value; // or whatever route you want to set the meta tags for

// This will set the meta tags based on the route
setMetaAttributes(to, from);
```

### Updating the current locale

```js
import { setLocaleToUse } from "@m-media/vue3-meta-tags";

setLocaleToUse("en");
```

### Setting your own JSON schema

You can set your own JSON schema for the page using the `jsonSchema` function.

```js
import { updateOrCreateSchema } from "@m-media/vue3-meta-tags";

const structuredData = {
  "@context": "https://schema.org/",
  "@type": "Dataset",
  name: Maps.map.value?.title ?? "Untitled map",
  description: Maps.map.value?.description,
  url: window.location.href,
  license: "https://creativecommons.org/publicdomain/zero/1.0/",
  isAccessibleForFree: true,
  creator: {
    "@type": "Organization",
    url: "https://app.cartes.io/",
    name: "Cartes.io",
  },
  distribution: [
    {
      "@type": "DataDownload",
      encodingFormat: "JSON",
      contentUrl:
        import.meta.env.VITE_API_URL + "/api/maps/" + Maps.map.value?.uuid,
    },
  ],
  temporalCoverage: Maps.map.value?.created_at + "/..",
};

updateOrCreateSchema(structuredData);
```
