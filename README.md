# KingdomJS

Kingdom is a lightweight javascript MVC that supports single and double bindings, template rendering and pub-sub. It works in all major browsers plus IE8.

## Getting started

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
By passing in properties when creating your Castle, those properties are immediately evaluated in the DOM.

```
template = new World.Castle
  name: 'myTemplate'
  properties:
    'property1': 'hello'
    'property2': 'world'
    ...
```

You can then read and write those properties within your Castle using Get / Set. 
Of course you can create properties anywhere else within your Castle using Get / Set. 
The difference is that upon innitialisation of your Castle the properties under `properties` 
will be immediately evaluated.

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

## Soldiers
Soldiers are functions that execute when they get an order. This follows the traditional pub-sub pattern and can be used to communicate between castles. For example, using orders you can order a soldier from another castle

```
template1 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template1'
  soldiers:
    'doSomething': (what) ->
      console.log what
  construct: ->
    @order 'template2.attack', 'bandits', false

template2 = new world.Castle
  # name of the template that this castle should bind to
  name: 'template2'
  construct: ->
    @order 'template1.doSomething', 'get arms', false
    @order 'attack'
  soldiers:
    'attack': (what) ->
      alert 'attacking ' + what + '!'
```

Soldiers are automatically namespaced to the Castle. So the soldier `attack` in template2 is being ordered from template1 as `@prder 'template2.attack'`

## Order
### order(soldier, data, namespace = true)

See example above. You order a soldier like this:
```
@order 'template1.doSomething', 'get arms', false
```

Orders are automatically namespaced to the template, so if you want to order a soldier in another template you must specify set namespace to `false`, and 
name your order `'templateName.soldier'`. Ordering soldiers within a template don't need namespacing, it's automatically being added.


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
Within bindings (and only within bindings) you can use mustache bindings like this:

```
template = new World.Castle
  name: 'myTemplate'
  properties:
    'property1': 'hello'
    'property2': 'world'

<div data-bind="template: myTemplate">
  <img data-bind="src: /t/{{property1}}/{{property2}}" />
</div>
```

will result in

```
<div data-bind="template: myTemplate">
  <img src="/t/hello/world" />
</div>
```


## Looping over templates
You can loop over a template using an array and a subtemplate like this

```
<div data-bind="array: preload">
  <div data-bind="template: preload">
    <div data-bind="if: model.is_slide">
      <div data-bind="if: isMobile">
        <div data-bind="background-image: model.medium_image_url"></div>
      <div data-bind="else">
        <div data-bind="background-image: model.large_image_url"></div>
      </div>
    </div>
  </div>
</div>
```

The important thing is that the sub-template name must be exactly the same as your array name. 
Within the subtemplate, if you want to bind to array values, you must prefix those values with `model`

## Set and Get properties
You can create, get and set Castle properties like this

```
@set 'myProperty', 'hello'
console.log @get('myProperty')    # logs 'hello'
```

When you set properties the corresponding template bindings are reevaluated and the DOM is being updated accordingly.
You can use this to create reactive interfaces.

## toggleProperty
You can toggle a boolean property like this

```
@toggleProperty 'boolean'
```

## Hide template
You can hide a template like this

```
@hide()
```

## Show only this template
You can hide all other templates except this one

```
@uberCastle()
```

# Dependencies
Kingdom needs Jquery and UnderscoreJS. Underscore is mainly required to be IE8 compatible.


# Compatibility
KingdomJS works in Chrome, Safari, Firefox and IE8-12
