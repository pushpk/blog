---
title: Sharing Data Between Angular Components using Services
date: 2020-05-05 00:34:00 +0800
categories: [Blogging, Tutorial]
tags: [Angular, Web Development]
toc: false
seo:
  date_modified: 2020-05-16 14:30:11 -0500
---
{:refdef: style="text-align: center;"}
![upload-image](/assets/img/commons/Angular_Components.jpg)
{: refdef}

Welcome to Part-2 of Sharing Data between components in Angular <br> 

This is Part 2 of 2-part series:        <br>


[Part 1: Sharing Data from Parent to Child Component and Child to Parent] (http://pushpk.com/posts/Sharing-Data-Between-Angular-Components/)
**[Part 2: Sharing Data between non-parent-child(siblings) related components] (http://pushpk.com/posts/Sharing-Data-Between-Angular-Components-2/)**    


In Previous Post, We have seen how can we can use @Input, @Output and @Viewchild to share data between Parent-Child Components, In this post, we will talk about how we can share data between components those are not related. <br><br>

For Components those are not related, we can provide them common place where data exchange can happen, and that common place is Services.related. <br><br>

Components can talk to each-other via services, injected in components.related. <br><br>

[Here](https://stackblitz.com/edit/angular-communicating-between-components-using-services) is link to demo project of Product and Cart Components, our goal is to send product to cart when user clicks on 'Add to Cart'related. <br><br>

Product Component

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

```html
<div class="product">
	<div class="card">
		<img src="https://cdn.pixabay.com/photo/2014/04/09/09/56/marbles-319938_960_720.jpg" alt="Denim Jeans" style="width:100%">
		<h3>Marbles</h3>
		<p class="price">$19.99</p>
		<p><button class="btn btn-primary">Add to Cart</button></p>
	</div>
</div>
```

Cart Component

```html
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

So product component has  product that we want to pass it to Cart component, Let us create new service under services folder with one method

Service will have 2 methods, SetCaetData and GetCartData, 

Name of methods are self-explantory, one sets cart data and other one gets cart data. Here is code for those methods:

```typescript
import { Injectable } from '@angular/core';
import { Observable, Subject } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ProductService {
    ProductName : string;
    Price: number


    SetCartData(productName: string, price: number) {
    this.ProductName = productName,
    this.Price = price
    }

    GetCartData() {
       return { productName : this.ProductName, price : this.Price}
    }
}
```

Now let us use those methods in our components, first, send data from product component to service to set data.

```typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../_services/Product.service';

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit {

  constructor(private productService: ProductService) { }
  AddtoCart(productName: string, Price: number) {
    this.productService.SetCartData(productName, Price);
  }
  ngOnInit() {
  }
}
```

```html
<div class="product">
  <div class="card">
  <img src="https://cdn.pixabay.com/photo/2014/04/09/09/56/marbles-319938_960_720.jpg" alt="Denim Jeans" style="width:100%">
  <h3>Marbles</h3>
  <p class="price">$19.99</p>
  <p><button class="btn btn-primary" (click)="AddtoCart('Marbles', 19.99)">Add to Cart</button></p>
</div>
</div>
```
After bringing ProductService in Constructor, we can use it  on button click to add data to cart, for simplicity I am passing static values to AddtoCart method, <br><br>

Now that we have set data in Service, We can easily consume and show it in cart when user click on "Show cart" <br><br>

Here is how cart component look like: 

```typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../_services/Product.service';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent implements OnInit {
  productName: string;
  Price: number;

  constructor(private productService: ProductService) { }

  ngOnInit() {}
  showcart() {
    const product = this.productService.GetCartData();
    if (product) {
      this.productName = product.productName;
      this.Price = product.price

    }
  }
}
```

```html
<div class="cart"><br />
<a href="#" (click)="showcart()" >Show Cart</a>
<div class="card" *ngIf="productName">
  <div>Product Name : {{productName}}</div>
  <div>Product Price : {{Price}}</div>
</div>
</div>
```

Same as Product component, After injecting service into constructor, we can use GetCartData method of service to bring Product Data and show it on UI

Please [click here](https://stackblitz.com/edit/angular-communicating-between-components-using-services) to see demo and source code  <br><br>


{:refdef: style="text-align: center;"}
![angular_components](/assets/img/commons/angular_components.gif)
{: refdef}   


Thank you.