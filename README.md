# KingdomJS

Kingdom is a lightweight javascript MVC that supports single and double bindings, template rendering and pub-sub.

## Get started

.coffee file
```
Kingdom.create 'world'

template = new world.Castle
  # name of the template that this castle should bind to
  name: 'start'
  
  # properties (vars) in your castle
  properties:
    'messages': 'hello world'
    'loading': false
    
  # plain functions
  
  functions:
    changeMessage:(newMessage) ->
      @set 'message', newMessage
      
  # soldiers are functions that listen to orders
  soldiers:
    'stopLoading': ->
        @set 'loading', true
        @changeMessage 'done!'
        
  # everything that needs to be execute first
  construct: ->
    @set 'walls', 'build them!'
    
    # call the soldier
    @order 'stopLoading'
```

html
```
<div template="start'>
  <div data-bind="if: loading">
    <h1>Loading</h1>
  <div data-bind="else">
    <p data-bind="text: message"></p>
  </div>
</div>
```

## Supported bindings
### attribute bindings
"disabled", "text", "src", "if", "unless", "array", "href", "id", "value", "height", "width"

### event bindings
"click", "hover", "mouseover", "mouseleave"

### add your own
everything that's not in these lists will be treated as an attribute binding

## If / Unless / Else
You can do if/else and unless/else like this

```
<div data-bind="if: show">
  <p>show</p>
</div>
<div data-bind="else">
  <p>don't show</p>
</div>
```

```
<div data-bind="unless: show">
  <p>show</p>
</div>
<div data-bind="else">
  <p>don't show</p>
</div>
```

## Mustache bindings
Within bindings you can use mustache bindings like this:
`img data-bind="src: /t/{{path}}/{{key}}"`

## Loop over template
You can loop over a template using an array and a subtemplate like this

```
<div data-bind="array: preload">
  <div data-bind="template: preload">
    <div data-bind="if: model.is_slide">
      <div data-bind="if: ENV.app.is_mobile">
        <div data-bind="background-image: model.medium_image_url"></div>
      <div data-bind="else">
        <div data-bind="background-image: model.large_image_url"></div>
      </div>
    </div>
  </div>
</div>
```

The important thing is that the template name must be the same as the array name. 
Within the subtemplate you must refer to array values by referring and prefixing to `model`

# Set and Get
You can get and set Castle properties like this
```
@set 'myProperty', 'hello'
@get 'myProperty'
```

When you set properties the corresponding template bindings are reevaluated and the view is being updated

# toggleProperty
You can toggle a boolean property like this
```
@toggleProperty 'boolean'
```

#soldiers
Soldiers are functions that execute when they get an order. This follows traditional pub-sub pattern. Use soldiers to communicate between castles. 

```
template1 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template1'
  soldiers:
    'template2.doSomething': (what) ->
      console.log what

template2 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template2'
  construct: ->
    @order 'doSomething', 'hello'
```

Soldiers are automatically namespaced, so in the example you see that the soldier in template1 only listens to orders from  template2

# hide template
You can hide a template like this
```
@hide()
```

# show only this template
You can hide all other templates except this one
```
@uberCastle()
```
