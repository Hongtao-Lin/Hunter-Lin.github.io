---
layout: post
title: "html css revisit"
date: 2016-07-23 17:13:15
comments: true
published: true
description: "HTML, CSS, Basic"
keywords: "HTML, CSS"
excerpt_separator: <!--more-->
categories: web
tags: 
- web
---

Introduce and sum up typical technique/hack used in CSS, like vertical alignment, flex display.
<!--more-->

# CSS 

## Highlights

1. `zoom: 1` triggered IE 6/7 HasLayout.
2. Adaptable width + Fixed width: use `margin-left: <fixed-width>`.
3. To allow a div to span across , use `top:0, bottom` 

## Q: How to make a div vertically in the middle?

A: 

For text:

Try using same padding for top and bottom first, if not working:
If align a single line of text within a block, use `line-height: 50px; height: 50px;`
If multiple lines, try using `display: table` or `display: flex; justify-content`

For blocks:

If the height of element is known: use `top: 50%; margin-top: -50px;` 
Or when it's unknown: `trasform: translateY(-50%);` -- Does it hurt performance?
Or use flexbox: `flex-direction: column; jusrtify-content: center;`

## Q: What does `display: table` do?

A: 

It can be used to do vertical alignment automatically.  Also, combined with `margin: auto` makes horizontally aligned elements!
Another good property is the change of order as you will. For elements in table, we can set the `display` as `table-caption`, `table-footer-group`, `block` to dynamically change the order of stack. 
See [http://colintoh.com/blog/display-table-anti-hero] for more detail!

## Q: What does `display: flex` do?

A: 
It is a new property in CSS3, supporting all kinds of demand on multiple-item placement. Also very useful in responsive design.
Note that for IE, only IE10+ support this property.   
See [https://css-tricks.com/snippets/css/a-guide-to-flexbox/] for more detail! 

## Q: Why using `border` right/left/bottom can draw a triangle?

A: 
Have you ever considered the overlapped area in the corner, when border-left/top are both provided? That's where the problem lies! The overlapped area is divided into two triangles. 
Once the width and height are set to be zero, the borders meet together.

## Q: What is iconfont?

A: 

A quick and easy way to place icons! There are many iconfont providers including: [https://metroui.org.ua/font.html]{Microsoft metro ui} and [http://fico.lensco.be/]{Fico}, which are charged :(. Also There are free ones introduced in [https://css-tricks.com/flat-icons-icon-fonts/]{here}

## Q: Use `<button>` or `<div>` instead?

A: 

Sorry, I made a mistake: better not use `<div>` for clickable things, use `<button>` or `<a>` is okay. Cuz they convey the message that they can be clicked, but div do not have this semantic meaning. 

## Q: Is it suitable to use `box-sizing: border-box` ?

A:

Just embrace it! It's just a great idea!

## Q: What can be horizontally aligned using `text-align: center`?

A: 

Not sure :(

## Q: How to split a div into several part with intermediate margin?

A: Use `display: flexbox` or simply `text-align: justify`

## Q: How's `vertical-align` performs?

A: 

## Q: What is CSS Sprite?

## Q: Difference btw. dl, ol, ul?
