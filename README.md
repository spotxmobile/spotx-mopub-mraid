# SpotX - MoPub MRAID Creative Script

The SpotX MoPub MRAID creative script should be used to create MRAID creatives in the MoPub interface.
The `mraid.html` file in this repository contains the entire script necessary to define a MoPub MRAID creative.
The script acts as a "loader", which will handle making the request to the SpotX network to obtain a
proper MRAID compliant advertisement.

##Usage
To use the script, copy the *entire* text from a script file into the `'HTML Body'` section
of the MRAID creative definition.  You may use either version of the script (verbose or minified)
since the functionality is identical.   

![MoPub MRAID Definition Screenshot](screenshots/screen1.png)

> Always make sure to check the `'MRAID Ad'` option.

To customize the script you should only edit the `data-...` parameters of the `spotx-mopub-mraid` script tag.
```html
<!-- do not copy this sample, copy the contents of the mraid.html file above! --->
<script id="spotx-mraid-loader" type="text/javascript"
    data-channel-id="85394"
    data-request-params="app[name]=myapp&app[domain]=myapp.mydomain.com&app[bundle]=com.mydomain.myapp&device[devicetype]=1&device[ifa]=%eudid!&device[dnt]=%%DNT%%&cb=%%CACHEBUSTER%%"
    data-request-timeout="8"
    data-failover-script='loaded=true; window.location="mopub://failLoad";'
    src="http://m.spotx.tv/mraid/v1/spotx.js"
></script>
```

| Parameter              | Description |
|------------------------|-------------|
|`data-channel-id`       | The SpotX Channel ID to use for the SpotMarket request. |
|`data-request-params`   | Name value pairs after the `'?'` in the SpotMarket request url. |
|`data-request-timeout`  | The number of seconds to wait for the SpotMarket request to complete. |
|`data-failover-script`  | The MoPub script called when a failover condition occurrs.<br/><br/>**Do not modify this parameter**, unless there are changes to the current MoPub MRAID container specification - see the Failover tag section in the MoPub Custom Networks document. |
|`src`                   | The url to the SpotX `mopub.js` script file.<br/><br/>**Do not modify this parameter**, unless the location or version of the script file changes. |


In the current `mraid.html` and `mraid.min.html` files you must update the `data-channel-id` and `data-request-params` parameters with the proper information. 
```html
<script id="spotx-mraid-loader" type="text/javascript"
  data-channel-id="REPLACE_CHANNEL_ID"
  data-request-params="app[name]=REPLACE_ME&app[domain]=REPLACE_ME&app[bundle]=REPLACE_ME&device[devicetype]=1&device[ifa]=%eudid!&device[dnt]=%%DNT%%&cb=%%CACHEBUSTER%%";
    ...
></script>
```
You must replace the `REPLACE_CHANNEL_ID` text with the SpotX Channel ID you want to syndicate, and the `REPLACE_ME` text
with the proper values, described in the [SpotMarket MRAID Request Parameters](#spotmarket-mraid-request-parameters) section.

##SpotMarket MRAID Request Parameters
The SpotMarket MRAID url contains several parameters (specified in the `data-request-params` parameter) that must be specified in order to create the proper MRAID response.  When creating the url, you may also use
macros provided by MoPub for the parameter values.  These macros will be substituted with their corresponding values when the script is obtained by the client's MRAID container.
For more information about the MoPub Macros, check out the [MoPub Documentation](https://dev.twitter.com/mopub/ui/macros). 
The following table contains the currently supported parameters:

###Application Data
|Parameter             | Description / Example | Type | Usage |
|----------------------|-----------------------|------|-------|
|`app[name]`           | Defines the name of the application.<br/><br/>`app[name]=mygame` | String | Required |
|`app[domain]`         | Domain of the application.<br/><br/>`app[domain]=mygame.foo.com` | String | Required |
|`app[bundle]`         | Defines the application bundle or package name. This is intended to be a unique ID across multiple exchanges.<br/><br/>`app[bundle]=com.foo.mygame` | String | Required |
|`app[cat]`            | Array of IAB content categories for the overall application.<sup>1</sup><br/><br/>`app[cat]=IAB6-8` | Array | Recommended |
|`app[storeurl]`       | For QAG 1.5 compliance, an app store URL for an installed app should be passed in the bid request.<br/><br/>`app[storeurl]=http%3A%2F%2Fplay.google.com%2Fmygame` | String | Recommended |
|`app[ver]`            | Application version.<br/><br/>`app[ver]=1.2.1` | String | Optional |
|`app[privacypolicy]`  | Specifies whether the app has a privacy policy. “1” means there is a policy and “0” means there is not.<br/><br/>`app[privacypolicy]=1` | Integer | Optional |

###Device Data
|Parameter              | Description / Example | Type | Usage | MoPub Macro |
|-----------------------|-----------------------|------|-------|-------------|
|`device[devicetype]`   | Defines the type of device being used <sup>1</sup><br/><br/>`device[devicetype]=1` | Integer | Required | |
|`device[dnt]`          | If “0”, then do not track Is set to false, if “1”, then do no track is set to true in browser.<br/><br/>`device[dnt]=%%DNT%%`| String | Required | `%%DNT%%` |
|`device[ifa]`          | ID sanctioned for advertiser use in the clear, not hashed (i.e., Android Advertising ID or Apple IDFA for iOS).<br/><br/>`device[ifa]=%eudid!` | String | Required | `%eudid!`<sup>2</sup> |
|`device[dpidsha1]`     | Platform device ID (i.e., Android Device ID); hashed via SHA1. Only use for Android Apps supporting MRAID that do not support Google Play Services.<br/><br/>`device[dpidsha1]=%eudid!` | String | Optional |`%eudid!`<sup>2</sup> |
|`device[geo][lat]`     | Latitude from -90 to 90. South is negative. This should only be passed if known to be accurate<br/><br/>`device[geo][lat]=%%LATITUDE%%` | Float | Optional<sup>3</sup> | `%%LATITUDE%%` |
|`device[geo][lon]`     | Longitude from -180 to 180. West is negative. This should only be passed if known to be accurate.<br/><br/>`device[geo][lon]=%%LONGITUDE%%` | Float | Optional<sup>3</sup> | `%%LONGITUDE%%` |

###Site Data
|Parameter              | Description / Example | Type | Usage | MoPub Macro |
|-----------------------|-----------------------|------|-------|-------------|
|`cb`                   | Cache Buster - Defines a dynamically generated random number.<br/><br/>`cb=%%CACHEBUSTER%%` | Integer | Required | `%%CACHEBUSTER%%` |
|`site[cat]`            | Array of IAB content categories of the site.<sup>1</sup><br/><br/>`site[cat]=IAB6-8` | Array | Recommended | &nbsp; |

> <sup>1</sup> The parameter values may be found in the [OpenRTB API Specification 2.2](http://www.iab.com/wp-content/uploads/2015/06/OpenRTBAPISpecificationVersion2_2.pdf).<br/>
> <sup>2</sup> IDFA, Android Advertising ID (if Google Play Services is integrated), or SHA1 Hashed Android Device ID (if Google Play Services is unavailable).  Macro typically used for the `device[ifa]` parameter.<br/>
> <sup>3</sup> While `device[geo][lon]` and `device[geo][lat]` are optional, if either parameter is specified, then the other parameter must be specified.  The substitution of these
values may not occur if the MRAID container app does not provide location services. 

###SpotX Custom Parameters
There are additional parameters that are custom to SpotX.  These parameters are required, and will be automatically appended to the SpotMarket request url.
**You should not specify these parameters.**  The details of these parameters are provided here for completeness.

|Parameter              | Description | Default Usage |
|-----------------------|-------------|---------------|
|`autoinit`             | Automatically initialize the MRAID creative and prepare for playback. | `autoinit=1` |
|`autoplay`             | Automatically begin playing the creative when ready. | `autoplay=1` |
|`prefetch`             | Fetch the creative on the server, at the time of the request. | `prefetch=1` |

##Error Handling - MoPub Failover
MoPub provides a custom failover tag that may be executed in certain error conditions, such as network
timeout, or no eligible ad available (no fill).  The MRAID script will automatically call the MoPub failover
tag when these types of errors are detected.

The failover tag will be called in the following instances:

* SpotMarket request timeout
* Invalid SpotMarket Channel (HTTP 204 error)
* Invalid SpotMarket URL (HTTP 404/403 errors)
* Disabled Channel
* VPAID *AdError* (for Opt-Out conditions) <sup>4</sup>

> <sup>4</sup> If a VPAID *AdError* occurs before the VPAID *AdImpression* event is fired, the failover tag
will be called.  This situation will typically occur if the MRAID ad contains a VPAID creative that decides
not to bid on the slot and errors out, before the impression.  If the *AdError* event occurs after the
*AdImpression*, the failover tag will not be issued since the ad impression has already been counted. 
