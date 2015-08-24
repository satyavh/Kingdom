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
<div template="start">
  <div data-bind="if: loading">
    <h1>Loading</h1>
  </div>
  <div data-bind="else">
    <p data-bind="text: message"></p>
  </div>
</div>
```

## Initialize your Kingdom
### Kingdom.create(name)
```
Kingdom.create 'World'
```

After that your Kingdom will be named 'World' and everything is namespaced as 'World'.
You can create as many Kingdoms as you like

## Create a Castle
### template = new [kingdomName].Castle

A Castle is the model/controller for a template (the view). You create a castle like this. The name is required and refers to the template name as stated in the html

```
template = new World.Castle
  name: 'myTemplate'
  
  
<div data-bind="template: myTemplate">
</div>
```

## Properties
By passing in properties when creating your Kingdom, those properties are immediately evaluated in the DOM.

```
template = new World.Castle
  name: 'myTemplate'
  properties:
    'property1': 'hello'
    'property2': 'world'
    ...
```

You can then read and write those properties within your Castle using Get / Set

```
@set 'property1', 'bye'
console.log @get('property2')
```

## Construction
Everything in the construct function will run (once) upon creating the Castle 

```
template = new World.Castle
  name: 'myTemplate'
  properties:
    'property1': 'hello'
    'property2': 'world'
    ...
  construct: ->
    @set 'walls', 'build!'
```

## Functions
Within your castle you can define as many functions, which can be called using `@functionName`

```
template = new World.Castle
  name: 'myTemplate'
  properties:
    'property1': 'hello'
    'property2': 'world'
    ...
  construct: ->
    @set 'walls', 'build!'    
  functions:
    helloWorld: ->
      console.log @get('property1') + ' ' + @get('property2')
```

##soldiers
Soldiers are functions that execute when they get an order. This follows the traditional pub-sub pattern and can be used to communicate between castles. For example, using orders you can order a soldier from another castle

```
template1 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template1'
  soldiers:
    'template2.doSomething': (what) ->
      console.log what
  construct: ->
    @order 'template1.attack'

template2 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template2'
  construct: ->
    @order 'doSomething', 'hello'
    @order 'attack'
  soldiers:
    'attack': ->
      alert 'attacking!'
```

Soldiers are automatically namespaced, so in the example you see that the soldier in template1 only listens to orders from template2. Ordering soldiers within a template don't need namespacing (then again, you can just use functions within a template)

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

## Looping over templates
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

## Set and Get
You can get and set Castle properties like this
```
@set 'myProperty', 'hello'
@get 'myProperty'
```

When you set properties the corresponding template bindings are reevaluated and the view is being updated

## toggleProperty
You can toggle a boolean property like this
```
@toggleProperty 'boolean'
```

## hide template
You can hide a template like this
```
@hide()
```

## show only this template
You can hide all other templates except this one
```
@uberCastle()
```

