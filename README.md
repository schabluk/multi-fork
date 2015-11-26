# multi-fork

Fork a stream into multiple streams.

## installation

```npm install multi-fork```

## example

Code:

```javascript
var MultiFork = require("./")
var duplexer = require('duplexer2')
var _ = require('highland')
var u = require('underscore')

var streamJohn = _().each(function(data) {
      console.log(_.extend(data, {sendTo: 'John'}))
    })
var streamAnna = _().each(function(data) {
      console.log(_.extend(data, {sendTo: 'Anna'}))
    })
var streamBill = _().each(function(data) {
      console.log(_.extend(data, {sendTo: 'Bill'}))
    })

var outputStreams = [streamJohn, streamAnna, streamBill]

var docs = [
  {type: 'Apple'},
  {type: 'Banana'},
  {type: 'Coco'},
  {type: 'Coco'}
]
var partitionByKey = 'type'
var partitionRanges = ['Apple', 'Banana', 'Coco']

var classifier = function(input, cb) {
  var index = u.indexOf(partitionRanges, input[partitionByKey])
  return cb(null, index)
}

var multiStream = new MultiFork(outputStreams.length, {
  classifier: classifier
})

for (var index in multiStream.streams) {
  multiStream.streams[index].pipe(outputStreams[index])
}

_(docs).pipe(multiStream)
```

Output:

```
$ node example.js
{ sendTo: 'John', type: 'Apple' }
{ sendTo: 'Anna', type: 'Banana' }
{ sendTo: 'Bill', type: 'Coco' }
{ sendTo: 'Bill', type: 'Coco' }
```

## license
-------

3-clause BSD. A copy is included with the source.
