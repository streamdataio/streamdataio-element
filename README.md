# streamdataio-element

A web component to stream APIs through [Streamdata.io](http://www.streamdata.io) proxy in Polymer.

## Setup

In order to use the Streamdata.io proxy, you need an App Token. Register on the [Developer Portal](https://portal.streamdata.io/#/register) to create your free account and get a valid token by creating a new application.

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
    auto
    url="http://your-api.com/"
    response-data-type="object"
    is-active="{{isActive}}"
    on-response="onResponse">
</streamdata-io>
```
```js
<script>
   Polymer({
    is: 'my-real-time-component',
    ...
    onResponse: function (evt, detail) {
        this.myObjectToRefresh = detail.data;
        ...
    }
   )}
</script>
```

Parameters
* `token`: the Streamdata.io token of your application.
* `auto`: a boolean parameter to automatically start the streaming session when `is-active` is true.
* `url`: the url of the API to be streamed.
* `response-data-type`: the type of data returned by the API. The two values accepted are `array` and `object`.
* `is-active`: a boolean parameter to start or close the Server-Sent Event session.
* `on-response`: the method to call when a new `response` event is fired by the component.

Note: The params attribute must be double quoted JSON.

## More options

### Dealing with Arrays

If the API you're streaming sends `array`, you'll need to override the dirty checking performs by Polymer in version 1.0 to dynamically update your display:

```HTML
<streamdata-io token="your-token"
    auto
    url="http://your-api.com/"
    response-data-type="array"
    is-active="{{isActive}}"
    on-response="onResponse">
</streamdata-io>
```
```js
<script>
   Polymer({
    is: 'my-real-time-component',
    properties: {
        myArrayToRefresh: {
            type: Array,
            value: function () {
                return [];
            }
        }
    },
    ...
    onResponse: function (evt, detail) {
        var arrayToRefresh = detail.data;
        this.set("myArrayToRefresh", []);
        this.set("myArrayToRefresh", arrayToRefresh);
        ...
    }
   )}
</script>
```

See [override dirty checking](https://www.polymer-project.org/1.0/docs/devguide/model-data#override-dirty-check) in Polymer documentation for more information.

### Handle initial snapshot and incremental updates separately

In case you need to handle the initial snapshot of data and incremental updates in different methods, you can listen to the `snapshot` and `patch` events instead of `response`:

```HTML
<streamdata-io token="your-token"
               auto
               url="http://your-api.com/"
               response-data-type="object"
               is-active="{{isActive}}"
               on-snapshot="onSnapshot"
               on-patch="onPatch">
</streamdata-io>
```

Don't forget to use the `jsonpatch` library embedded in the component to apply updates:

```js
<script>
   Polymer({
    is: 'my-real-time-component',
    ...
    onSnapshot: function (evt, detail) {
        this.myObjectToRefresh = detail.data;
        ...
    },
    onPatch: function (evt, detail) {
         this.myObjectToRefresh = jsonpatch.applyPatch(this.myObjectToRefresh, patch).newDocument;
         ...
    }
   )}
</script>
```

### Listen to path changes

If you want to use your component with different paths for the same resource, you can pass them dynamically to the `streamdata-io` element with the `path` attribute:

```HTML
<streamdata-io token="your-token"
               auto
               url="http://your-api.com/"
               path="{{path}}"
               response-data-type="object"
               is-active="{{isActive}}"
               on-response="onResponse">
</streamdata-io>
```

On each changes of the path, the streaming session will automatically be closed and reopened with the new path.

### Manually create the streaming session

If one or more parameters of your component attributes are dynamically injected, it's safer to set the `auto` parameter to `false`. Otherwise the streaming session might start without all needed parameters as you can't predict in which order those parameters will be loaded.

To set the `auto` parameter to `false`, simply remove it from your component declaration:

```HTML
<streamdata-io id="streamdataIo"
               token="your-token"
               url="http://your-api.com/"
               response-data-type="object"
               is-active="{{isActive}}"
               on-response="onResponse">
</streamdata-io>
```

And then, add a new method to call the `createEventSource` method on your component by calling its id:

```js
<script>
   Polymer({
      is: 'my-real-time-component',
      ...
      createSession: function () {
        this.$.streamdataIo.createEventSource();
      },
      onResponse: function (evt, detail) {
        this.myObjectToRefresh = detail.data;
        ...
    }
   )}
</script>
```

### Use HTTP Headers

If the API you're streaming through Streamdata.io requires HTTP Headers to be accessed, you can provide them as a JSON Array object to the `streamdata-io` component with the `httpHeader` parameter:

```HTML
<streamdata-io token="your-token"
               auto
               http-headers='["header1:header1Value", "header2:header2Value"]'
               url="http://your-api.com/"
               response-data-type="object"
               is-active="{{isActive}}"
               on-response="onResponse">
</streamdata-io>
```