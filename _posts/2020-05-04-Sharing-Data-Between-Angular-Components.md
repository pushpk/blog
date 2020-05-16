---
title: Sharing Data Between Angular Components
date: 2020-05-04 00:34:00 +0800
categories: [Blogging, Tutorial]
tags: [Angular, Web Development]
toc: false
seo:
  date_modified: 2020-05-10 14:30:11 -0500
---
{:refdef: style="text-align: center;"}
![upload-image](/assets/img/commons/Angular_Components.jpg)
{: refdef}


As Angular Project Grows and you start to divide your code into multiple components, there will be need to pass data around between components,   

Now these components could be related or not, and where I say related, I specifically mean, Parent-child or Child-Parent Relationship,
For related components first, following are different ways data can be passed   


*	@Input: sharing data from parent to child
*	@Output & EventEmitter: sharing data from child to parent   
*	@ViewChild: sharing data from child to parent



And for non-related components we can use services to share data.   


This is Part 1 of 2-part series:       


**[Part 1: Sharing Data from Parent to Child Component and Child to Parent](http://github.com)**    
[Part 2: Sharing Data between non-parent-child(siblings) related components](http://github.com)    



#### __@Input: sharing data from parent to child__

This is most common way to pass data from Parent to Child, on child component we need to decorate  field with @input decorator to let component know that value for this field will be coming from Parent Component,

Let’s take one example and through out discussion we will use same example:

Creating Parent Component:

<kbd>ng g c parent</kbd>

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
  styleUrls: ['./parent.component.css']
})
export class ParentComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

Creating child component

<kbd>ng g c child</kbd>

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
  styleUrls: ['./child.component.css']
})
export class ChildComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

Now let’s create simple textbox on parent component and label on child component with couple headers and goal is here to pass message from textbox to child component label

Parent component html:

```html
<div class="parent">
<h3>Parent Component</h3>
<input type="text" class="text-box" />
<app-child></app-child>
</div>

Child component html
<div class="parent">
<h3>Parent Component</h3>
<input type="text" class="text-box" />
<app-child></app-child>
</div>
```

This is how app looks after adding some bootstap class and styles, here is [stackbitz link](https://stackblitz.com/edit/angular-communicating-between-components-esu5rm) for reference

{:refdef: style="text-align: center;"}
![img-1](/assets/img/commons/img-1.jpg)
{: refdef}

Now in order to pass text-box value to child value label, we need to use @input() decorator like below on line 9 after importing it from angular core library on line 1:
Line 9 will indicate child component that message field will get its value from somewhere else where we render it.
Child Component:

```typescript
import { Component, OnInit, Input } from '@angular/core';

@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
  styleUrls: ['./child.component.css']
})
export class ChildComponent implements OnInit {
@Input() message : string;
  constructor() { }
  ngOnInit() {
  }
}
```

now we can easily bind that field from parent component where we are using child component tag like below  line 3: passing parentMessage value to message.

```html
<h3>Parent Component</h3>
<input [(ngModel)]="parentMessage" class="text-box">
<app-child [message]="parentMessage"></app-child>
</div>
```

With that we should get result like below:

{:refdef: style="text-align: center;"}
![img-2](/assets/img/commons/img-2.gif)
{: refdef}

#### **@Output & EventEmitter: sharing data from child to parent**

@output does opposite of @input, on parent @output is decorator on parent component property, basically its object of  EventEmitter

Bascially EventEmitter is way to tell Parent that child will be sending some data on some event like click or text change and that data will be captured on parent under event. let's understand through example:

I have added couple more fileds to above example: just like we had input on parent and lable on child, now we have input on child and label on parent:   



 here is [stackbitz link](https://stackblitz.com/edit/angular-communicating-between-components-esu5rm) for reference    




{:refdef: style="text-align: center;"}
![img-3](/assets/img/commons/img-3.jpg)
{: refdef}   


Let's understand lines we added : our goal is to send input value to Parent when someone starts entering into child:

On Child's input change event We need following line to trigger event. 

```html
  Message To Parent :<input [(ngModel)]="childMessage" class="text-box" (input)="updateMessage();"> 
```

in Child Component, Let's declare new field and decorate it with @Output that will emit this value to parent: 

```typescript
@Output() childMessageUpdated: EventEmitter<string> = new EventEmitter();
```

Be sure to import output and EventEmitter from @angular/core

now, let write event handler for input field that we defined in html that will emit value.

```typescript
  updateMessage(){
    this.childMessageUpdated.emit(this.childMessage)
  }
```
Simple event handler that is utilizing emit method of EventEmitter childMessageUpdated to pass value to Parent Component.

that's it for child component, let's go to Parent component now with keeping EventEmitter childMessageUpdated in mind.

On Parent component template, where we are declaring child component, we will be assigning our parent event handler to child EventEmitter like below:

```html
<div>
    <span>Message  From Child : {{childMessage}} </span>
</div>
<br />
<app-child [message]="parentMessage" 
(childMessageUpdated)="updateMessage($event)"></app-child>
</div>
```
this line will get data from child and pass it to updateMessage Event Handler on parent and parent will render it on lable as childMessage, 
now let's see how parent event handler look like

```typescript
 updateMessage(childMessageEvent : string){
    this.childMessage = childMessageEvent;
  }
```
 Simple event handle that takes string event value and setting it to childMessage

With all that here is how it looks like in action:


{:refdef: style="text-align: center;"}
![img-4](/assets/img/commons/img-4.gif)
{: refdef}   


### **@ViewChild: sharing data from child to parent**

Using instance of the child component into the parent as a @ViewChild is the another technique used by the parent to access the property and method of the child component

The @ViewChild decorator takes the name of the component/directive as its input. It is then used to decorate a property. The Angular then injects the reference of the component to the Property

here is [stackbitz link](https://stackblitz.com/edit/angular-communicating-between-components-esu5rm-viewchild) for reference    



so in above exmple, I have remove all output decorators and event code, here is stackbitz link,

on parent component lets create one field Child of childComponent and decorate it with @viewchild like below:

```typescript
   @ViewChild(ChildComponent) child: ChildComponent;
```
Bring in ChildComponent Import like below:

```typescript
    import {ChildComponent} from './child/child.component'
```
 now we can use, any property or methods of child component:

 
```html
       <span>Message  From Child : {{this.child.childMessage}} </span>
```

with all this changes, it will works same way as it was before:

Thank you!
