[![Build Status](https://img.shields.io/travis/ezsystems/ezplatform-http-cache.svg?style=flat-square&branch=master)](https://travis-ci.org/ezsystems/ezplatform-http-cache)
[![Downloads](https://img.shields.io/packagist/dt/ezsystems/ezplatform-http-cache.svg?style=flat-square)](https://packagist.org/packages/ezsystems/ezplatform-http-cache)
[![Latest release](https://img.shields.io/github/release/ezsystems/ezplatform-http-cache.svg?style=flat-square)](https://github.com/ezsystems/ezplatform-http-cache/releases)
[![License](https://img.shields.io/packagist/l/ezsystems/ezplatform-http-cache.svg?style=flat-square)](LICENSE)

# platform-http-cache

As of 1.12 default HTTP cache handling for [eZ Platform][ezplatform], and unlike the one bundled in [ezpublish-kernel][ezpublish-kernel]
and now deprecated, it centers on the use of (multi) cache tagging across Symfony Proxy _(using enhanced data store)_,
Varnish _(using [xkey][Varnish-xkey])_ and Fastly _(available for eZ Platform Cloud Enterprise users as of 1.13LTS)_.

It has been bundled with ezplatform since 1.8, and has been enabled by default since 1.12.

## Enabling the package on versions prior to 1.12
1. Add the package to `app/AppKernel.php`, *before* `EzPublishCoreBundle`, but after `FOSHttpCacheBundle`:

```php
    public function registerBundles()
    {
        $bundles = array(
            // ...
            new FOS\HttpCacheBundle\FOSHttpCacheBundle(),
            new EzSystems\PlatformHttpCacheBundle\EzSystemsPlatformHttpCacheBundle(),
            new eZ\Bundle\EzPublishCoreBundle\EzPublishCoreBundle(),
            // ...
        );
```

The package will replace several services from the kernel, thus enabling the new features, such as multi-tagging.

2. The application cache class needs to be customized. This is done by modifying `AppCache` class, you will have to make
extend `EzSystems\PlatformHttpCacheBundle\AppCache`.
    

Do not forget to restart your web server.

## Usage with Varnish

For usage with Varnish see the dedicated document in [docs/varnish](docs/varnish/varnish.md)


## Features

### `xkey` header on ContentView responses
Responses from `/content/view` will be tagged based on their contents:

```
curl -i -X HEAD 'http://localhost:8000/'

HTTP/1.1 200 OK
Host: localhost:8000
Connection: close
Cache-Control: public, s-maxage=60
Content-Type: text/html; charset=UTF-8
Vary: Cookie
Vary: Authorization
xkey: content-1
xkey: content-type-1
xkey: location-2
xkey: parent-1
xkey: path-1
xkey: path-2
```

### Toggling of cache on ContentView responses
Responses from `/content/view` will be made cachable, and the shared max age will be set if it is enabled.

### Purging of tagged HTTP cache on Repository operations
A set of Slots will send HTTP PURGE requests for each cache tag affected by write operations. 

[ezplatform]: http://github.com/ezsystems/ezplatform
[ezpublish-kernel]: http://github.com/ezsystems/ezpubish-kernel
[Varnish-xkey]: https://github.com/varnish/varnish-modules/blob/master/docs/vmod_xkey.rst
