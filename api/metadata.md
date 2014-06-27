---
layout: api
title: Metadata
api: true
order: 1
---

Metadata contains all of the information about a stream other than the logged data.  `title` and `description`
are good examples of information contained in stream metadata.

* toc
{:toc}

## Stream Schema

`phant-meta` modules all store stream metadata using different methods, but you will always recieve a stream
javscript object or an array of stream objects when requesting data.

### Default Schema

* `id` Unique identifier for the stream.
  * Integer or Hex String
  * Unique
* `title` User defined title for the stream.
  * String
  * Not unique
  * Required
* `description` User defined stream description.
  * String
  * Not unique
  * Required
* `hidden` User configurable flag to hide stream from public stream lists.
  * Boolean
  * Required
* `flagged` Flag for admins to hide streams from public stream lists due to objectionable content.
  * Boolean
* `fields` User defined field names that will be used when posting data.
  * Array
  * Required
* `tags` User defined tags that can be used to categorize streams.
  * Array
  * Optional
* `date` Automatically set by server when stream is created.
  * Date
* `last_pushed` Automatically set by server when new data is posted to the stream.
  * Date

## phant-meta Modules

There are a collection of `phant-meta` modules available in [npm](https://www.npmjs.org/search?q=phant-meta) which
all have the same interface.  The only difference between them should be the configuration for the storage mechanism
they each use.

### Properties

#### *name*
Used by phant to describe the metadata module when logging errors and other info.

### Methods

#### list (callback, [query], [offset], [limit], [sort])

* callback `Function`
  * Arguments
    * err `Mixed`
    * streams `Array` of `Objects`
* query `Object`
  * Optional
  * Default `{}`
* offset `Number`
  * Optional
  * Default `0`
* limit `Number`
  * Optional
  * Default `20`
* sort `Object`
  * Optional
  * Default `null`

Calls the `callback` with the arguments of `err` and `streams`. `streams` will be a JavaScript array of
objects with the [default schema](#default-schema) as properties. If `streams` is falsey, assume no streams matched
your query. If `err` is truthy, assume the call failed.

**Example** Retrieve a list of streams using the default settings

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.list(function(err, streams) {

    if(err) {
      return console.log('failed ' + err);
    }

    streams.forEach(function(stream) {
      console.log(stream.title);
    });

  });
{% endhighlight %}

**Example** Retrieve public streams 50-100 sorted by date descending

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.list(function(err, streams) {

    if(err) {
      return console.log('failed ' + err);
    }

    streams.forEach(function(stream) {
      console.log(stream.title);
    });

  }, { hidden: false }, 50, 50, { property: 'date', direction: 'desc' });
{% endhighlight %}

#### each (callback, [query], [offset], [limit], [sort])

* callback `Function`
  * Arguments
    * err `Mixed`
    * stream `Object`
* query `Object`
  * Optional
  * Default `{}`
* offset `Number`
  * Optional
  * Default `0`
* limit `Number`
  * Optional
  * Default `null`
* sort `Object`
  * Optional
  * Default `null`

Calls the `callback` with the arguments of `err` and `stream`. `stream` will be a JavaScript object
with the [stream schema](#default-schema) as properties. If `err` is truthy, assume the call failed.

The main difference between `list` and `each` is that `each` will call the callback one stream at a time until
it reaches the defined `limit`.  If no limit is defined, then it will continue calling the callback until it runs
out of streams that match the `query`.

**Example** Retrieve all streams using the default settings

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.each(function(err, stream) {

    if(err) {
      return console.log('failed ' + err);
    }

    console.log(stream.title);

  });
{% endhighlight %}

**Example** Retrieve flagged streams 50-100 sorted by last_pushed ascending

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.list(function(err, stream) {

    if(err) {
      return console.log('failed ' + err);
    }

    console.log(stream.title);

  }, { flagged: true }, 50, 50, { property: 'last_pushed', direction: 'asc' });
{% endhighlight %}

#### get (id, callback)

* id `Number` or `String`
* callback `Function`
  * Arguments
    * err `Mixed`
    * stream `Object`

Retrieves a specific stream by `id`. Calls the `callback` with the arguments of `err` and `stream`.
`stream` will be a JavaScript object with the [stream schema](#default-schema) as properties.
If `err` is truthy, assume the call failed.

**Example** Retrieve stream *1a2b3c*

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.get('1a2b3c', function(err, stream) {

    if(err) {
      return console.log('failed ' + err);
    }

    console.log(stream.title);

  });
{% endhighlight %}

#### create (data, callback)

* data `Object`
* callback `Function`
  * Arguments
    * err `Mixed`

Creates a new stream. `data` should always include the required portions of the [stream schema](#default-schema).
Returns a `callback` with `err` as the only argument. If `err` is truthy, assume the call failed.

**Example** Create a stream

{% highlight js %}

  var metadata = require('phant-meta-test')();

  var new_stream = {
    title: 'Weather Station',
    description: 'Monitors temp and rain in Uniontown, MD',
    hidden: false,
    fields: ['temp', 'rain'],
    tags: ['Uniontown']
  };

  metadata.create(new_stream, function(err, stream) {

    if(err) {
      return console.log('creation failed ' + err);
    }

    console.log('created');

  });
{% endhighlight %}

#### remove (id, callback)

* id `Number` or `String`
* callback `Function`
  * Arguments
    * err `Mixed`

Removes a specific stream by `id`. Calls the a `callback` with `err` as the only argument.
If `err` is truthy, assume the call failed.

**Example** Remove stream *1a2b3c*

{% highlight js %}
  var metadata = require('phant-meta-test')();

  metadata.remove('1a2b3c', function(err) {

    if(err) {
      return console.log('failed ' + err);
    }

    console.log('removed');

  });
{% endhighlight %}
