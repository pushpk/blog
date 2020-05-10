---
title: Sharing Data Between Angular Components
date: 2020-05-04 00:34:00 +0800
categories: [Blogging, Tutorial]
tags: [Angular, Web Development]
toc: false
seo:
  date_modified: 2020-05-04 13:12:27 -0500
---
{:refdef: style="text-align: center;"}
![upload-image](/assets/img/commons/Angular_Components.jpg)
{: refdef}


As Angular Project Grows and you start to divide your code into multiple components, there will be need to pass data around between components,
Now these components could be related or not, and where I say related, I specifically mean, Parent-child or Child-Parent Relationship,
For related components first, following are different ways data can be passed

*	@Input: sharing data from parent to child
*	@ViewChild: sharing data from child to parent
*	@Output & EventEmitter: sharing data from child to parent

And for non-related components we can use services to share data.   

This is Part 1 of 3-part series   

***[Part 1: Sharing Data from Parent to Child Component](http://github.com)***   
[Part 2: Sharing Data from Child to Parent Components](http://github.com)   
[Part 3: Sharing Data between non-parent-child(siblings) related components](http://github.com)

#### @Input: sharing data from parent to child

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

Thank you!
