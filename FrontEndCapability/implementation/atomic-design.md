# Atomic Design

Atomic design in software development is using smaller pieces, or components, to make bigger pieces 
which will make even bigger pieces and so on until we arrive at our deliverable.

Let's illustrate this with a real world example. If I were going to make a house I would need 
several components. At a bare minimum would need a foundation, some walls, and a roof.  I would 
also like to include items such as plumbing, electricity and gas.

Each of these components are made of smaller components.  The walls are going to have studs, 
drywall, paint, and windows.  Studs are going to need wood and nails.

Some of these components by themselves are not very useful.  A nail by itself does nothing.  With 
some wood, it can attach to a foundation and serve to complete my house.  I cannot have a house 
without these smaller pieces.

In web development we want to make a web page.  We can break a page down into components, and 
those into smaller components to achieve our goals.  If done right, we have reusable components 
that we can use in other places to give everything a cohesive look and feel, and predictable behavior.

The ideas of Atomic Design help us do that.


## Components

Components in software development would be the nails and studs in the analogy above.  They can be 
combined with other components to make bigger components.  With good design, they can be reused.  
This allows us to write cleaner code, and less code.  Less code is a great way to reduce bugs and 
problems in code.

For example, a carpenter will often have more than one nail, in fact he will have an entire box of 
nails.  If he needs a finishing nail, he will often reach into this container and grab one, 
without looking.  Why? Because to him it doesn't matter what nail he grabs, as long as it is a finishing nail.

We should write our components in the same way.   

For example, let's start with a simple a component.

     <input id="message-displayer">
     <button onclick="document.getElementById('message-displayer').value = 'The button was clicked'">
        Click Me
      </button>

This doesn't do much.  I click the button and the message "The button clicked" displays in the text box.  The potential for reuse is almost 0.

However, with a few changes and some abstraction can do:

    <input v-model="message" >
    <button v-on:click="$emit('button-clicked')">
        Click Me
    </button>

The above example uses Vue JS as an example.  But in simple terms it
 1. It will display whatever message is passed by the parent component
 2. The button will emit an event when clicked, and the parent can handle the event as it sees fit. 
 It can change the message in the box, or something completely different.
 
 The code is now much more reusable. Any where on the page that needs a button and a text field can 
 use this.

## Why Vue?

First off, you do not need Vue, or any framework to make reusable components, and use the principles of atomic design.  It is simply a tool to simplify the process. It is a means to an end, and not the end in and of itself.  For the rest of this guide, we will be using Vue JS, and vue files.

Vue helps us immensely with atomic design.  Vue will isolate our scopes, help control which components
can access or alter the state of other components, and for you CSS fans, it will isolate the styling of 
our components.

The above example rendered in a Vue file would look like this.

    <template>
      <div class="hello">
        <input v-model="message" >
        <button v-on:click="$emit('button-clicked')">Click Me </button>
      </div>
    </template>
    <script>
    export default {
      name: 'AwesomeComponent',
      props: {
        message: String
      }
    }
    </script>
    
    <style scoped>
    /*some css*/
    </style>


This at first glance adds a little complexity.  Some is to make this more reusable in the in future, 
and some is boilerplate needed by Vue.  However, this allows the parent component to dictate the behavior
of this component, while allowing it to be reused.  It also explicitly requires that some data be passed
into the component, in the `props` object.  

An excellent rule of thumb is that data is passed into the component via props, and data is emitted out
of the component via events, or `emit`

A parent calling this would look like this:

    <template>
      <div id="app">
        <awesome-component :message="awesomeMessage" v-on:button-clicked="awesomeMessage = 'Clicked'"/>
      </div>
    </template>
    
    <script>
    import AwesomeComponent from './components/AwesomeComponent.vue'
    
    export default {
      name: 'app',
      components: {
        AwesomeComponent
      },
        data () {
          return {
              awesomeMessage: ''
          }
        }
    }
    </script>
    
    <style>
      /* some css*/
    </style>

Notice I have to import the child component.  As the parent's data is changed, it is reflected in the 
child component via the message, and it receives the child components events and acts accordingly.

## A More Practical Example

I was creating a simple form.  However I noticed I was repeating the same group of elements over
and over again.

    <b-form @submit="getLetterTemplate" horizontal>
      <b-row>
        <b-col sm="3"><label for="template-id">Template Id:</label></b-col>
        <b-col sm="4">
          <b-form-input id="template-id" v-model="template.id" type="text"></b-form-input>
        </b-col>
      </b-row>
      <b-row>
        <b-col sm="3"><label for="template-name">Template Name:</label></b-col>
        <b-col sm="4">
            <b-form-input id="template-name" v-model="template.name" type="text placeholder="Template Name"></b-form-input>
        </b-col>
      </b-row>
      <b-row>
        <b-col sm="3"><label for="display-name">Display Name:</label></b-col>
        <b-col sm="4">
          <b-form-input id="template-name" v-model="template.displayName" type="text" placeholder="Display Name"></b-form-input>
        </b-col>
      </b-row>
      <b-button type="submit" variant="primary">Submit</b-button>
    </b-form>
    
I have essentially the same elements repeating over and over again, with just a few labels and field
text changing.  It's not very readable, admit it, you probably just skipped over it.  This is also very error prone
because if I want to change the behavior in how they act, I must change the behavior in each one, typo's
can creep in, or I can forget to change one (especially if this were going across several files).

Wouldn't this be much cleaner, readable and maintainable?

    <b-form horizontal @submit="getLetterTemplate">
      <letter-template-row row-id="template-id" label-text="Template Id" v-model="template.id"></letter-template-row>
      <letter-template-row row-id="template-name" label-text="Template Name" v-model="template.name"></letter-template-row>
      <letter-template-row row-id="description" label-text="Description" v-model="template.description"></letter-template-row>
      <letter-template-row row-id="template" label-text="Template" v-model="template.template" is-text-area="true"></letter-template-row>
       <b-button  type="submit" variant="primary">Submit</b-button>
    </b-form>
    
  With this in mind I can make the child component as follows:
  
      <template>
        <div>
          <b-row>
            <b-col sm="3"><label :for="rowId">{{labelText}}:</label></b-col>
            <b-col sm="4">
              <b-form-input v-if="!isTextArea" :id="rowId" v-model="localValue" type="text" :placeholder="labelText" v-on:input="onInput($event)" v-on:change="onChange($event)"></b-form-input>
            </b-col>
          </b-row>
        </div>
      </template>
      
      <script>
      export default {
        props: {
          rowId: {},
          labelText: {},
          value: {},
        },
        name: 'LetterTemplateRow',
        data: function () {
          return {
            localValue: this.value
          }
        },
        watch: {
          value: function value (newValue, oldValue) {
            if (newValue !== oldValue) { this.localValue = newValue }
          },
          localValue: function localValue (newValue, oldValue) {
            if (newValue !== oldValue) { this.$emit('input', newValue) }
          }
        },
        methods: {
          onInput: function (value) {
            this.localValue = value
          },
          onChange: function (value) {
            this.localValue = value
            this.$emit('change', this.localValue)
          }
        }
      }
      </script>
      <style scoped>
      </style>

The template for this component is the same as my expanded template from before, however now I do not
need to repeat myself.  It requires the parent to pass in (via props) the id for the label, the label
text.  `value` is a special property that binds to the model in the parent tag.  As the value in the 
child text field updates, it emits an event, and the parent changes it's models accordingly.

I can use this component now in forms all across my application, and give them the same look and feel.
It's changes are propagated across the app, and is much more maintainable.  

## Best Practices

1) Limit the scope of your variables as much as possible.  The more global a variable is, the easier it
is to change, and the harder it is to debug.

2) Components should only communicate events to their parents, and should only receive data 
from it's parent.  If you need to talk to siblings, or components in a different hierarchy that is a
good sign of an anti-pattern.

3) Components should not alter their state, rather they should alert their parent of an event and 
allow the parent to react to the event and change state as necessary.  If the component needs to alter its state
it should not alter the data passed into it, rather it should make it's own copy and alter that. As in the example above,
if the value of the text field changes, it does not alter the value passed in from the parent.  It alters
a local copy and informs the parent of the change, who can then take appropriate action. This helps safe guard, 
and isolate changes to state.