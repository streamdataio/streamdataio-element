# streamdataio-element

A web component to stream APIs through [Streamdata.io](http://www.streamdata.io) proxy in Polymer.

## Setup

In order to use the Streamdata.io proxy, you need an App Token. Register on the [Developer Portal](https://portal.streamdata.io/#/register) to create your free account and get a valid token by creating a new app.

## Installing with Bower

To install this component, run: 

```shell
$ bower install --save streamdataio-element
```

## Compatibility

This element is compatible with Polymer versions 1.x only.

## Basic Usage

```HTML
<streamdata-io token="your-token"
   url="http://your-api.com/"
   path="/path"
   response-data-type="array"
   is-active="{{isActive}}"
   on-snapshot="onSnapshot"
   on-patch="onPatch">
</streamdata-io>

...

<script>
   Polymer({
      is: 'my-real-time-component',
      ...
      onSnapshot: function (snapshot) {
         this.myObjectToRefresh = snapshot.detail.data;
         ...
      },
      onPatch: function (patch) {
         jsonpatch.apply(this.myObjectToRefresh, patch.detail.data);
          ...
      }
   )}
</script>
```

Parameters: 
* `token`: the Streamdata.io token of your application.
* `url`: the url of the API to be streamed.
* `path`: the path of the API to be streamed.
* `response-data-type`: the type of data returned by the API. The two values accepted are `array` and `object`.
* `is-active`: a boolean parameter to start or close the Server-Sent Event session.
* `on-snapshot`: the method to call when the first snapshot is received.
* `on-patch`: the method to call when incremental updates are received.

Note: The params attribute must be double quoted JSON.

## Demo

To see a full example of usage, check out the demo!