
You can head to [w3schools](https://www.w3schools.com/) to play with the basics of CSS. After you know what its about, you can follow my footsteps as follows. The source I referred was primarily [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS). Others will be hyperlinked along the way.


# 1. Simple CSS Selectors

The Simple CSS Selectors are used to select existing elements like `<p>`, `<div>`, etc. in the HTML DOM. After selected, you can style any behaviour of that element using [CSS properties](https://htmlcssmonk.medium.com/15-important-css-properties-every-developer-should-know-fbc44eb60757). Hence this is the most important & foundational concept in CSS.  
There are 4 types of Simple CSS Selectors:  
* [Common Selectors](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#common-selectors)
* [Child Selectors](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#child-selectors)
* [Sibling Selectors](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#sibling-selectors)
* [Attribute Selectors](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#attribute-selectors)


# 2. Advanced CSS Selectors

If you recall from Chapter 2, one motivation for learning CSS was being able to change the behaviour of such elements in the DOM on which an `event` like click or hover took place. This was often done by adding/removing/toggling additional `classes` within the `Event Handler method`. Those additional classes were styled in a CSS sheet. But what if we could include the `event handler` within the CSS code itself, reducing any JS code at all ? Amazed, right? Advanced CSS Selectors like [Pseudo-classes](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#pseudo-classes) & [Pseudo-elements](https://learn.shayhowe.com/advanced-html-css/complex-selectors/#pseudo-elements) allow us to do exactly that. 

To me, the `:hover` pseudo-class is of most utility. I personally don't find `:first-child`, `:last-child`, `:nth-child` pseudo-classes very helpful practically. Try out yourself to see.  

Among pseudo-elements, I find `li::marker` quite useful as I can replace bullet points of li with emojis :). `::before` and `::after` can also be used on a box, block or card parent element to design it with special effects like [halo border](https://pastebin.com/AST0vhBM), [spinning border](https://pastebin.com/hJrjM9aM) and [ribbon corner-badge](https://pastebin.com/R0KATNV8) . You can also create subtle `gradient shadows` and `gradient backgrounds` on the parent element as illustrated in [this video](https://www.youtube.com/watch?v=QFjqxVMwIl8) and [this video](https://www.youtube.com/watch?v=ezP4kbOvs_E), going a step further than a simple `box-shadow` property.

Note that it is necessary to use the `position: relative;` property on a parent element when you want to position a pseudo-element (like ::before) absolutely within that parent.


# 3. Box

Do you remember that as a child, you used to write your mini-notes and important math formulae within boxes, to emphasize them. Your webpage is also like your notebook. If you look, all content is arranged in boxes - the header, the hero section, the projects section, the footer, etc. A box is nothing but a `<div>` with some content. You give it a height & width, fill it with some color, give it a border and there it is ! There's a lot you can do with this box:  
* Position it in the webpage
* Width control
* Decorative content font & borders
* Custom layout of children items in flex or grid
* Cool animations using CSS :hover & JS

The possibilities are endless. Among all, mastering width control of a container for responsive screen sizes is quite powerful. This is because in a webpage, most boxes are arranged horizontally. Vertical scrolling is quite normal. But horizontal overflow is bad design. 

The default value is the `width: auto` property which instructs the container to adjust its width automatically based on its content. **That's dangerous** as it can easily lead to horizontal overflow of the container beyond the viewport. So I always set it as some % (not px) of the parent viewport like 100% or 90%, eg. `width: 100%`.  

Still the children items inside the container can overflow horizontally outside the container. So if it's a flex container, ensure that `flex-wrap` is activated. Further to wrap certain no. of items on a certain screen size, you can use `flex-basis` property on the child items, eg. `@media-query(max-width:768px){flex-basis: 50%;}`  ensures two items wrapped per line of viewport on mobile screens.

Also ensure that the container as well as each child item inside it has sufficient padding eg. `padding: 1rem`. See that you use the `box-sizing: border box` property on every padded element to prevent the padding from increasing the width of the element causing overflow issues.  

For most boxes, you'd want it to be centred horizontally. For that I prefer the `margin: 0 auto` property. You may think that when width: 100%, the auto value might shrink to 0. True. But `html, body` usually have inbuilt default css margins preventing your content from touching the edges of the viewport. If you still think you need to add some margin say 1rem on both left and right for extra safety use `margin: 1rem` alongwith `width:calc(100% - 2rem)` on your container. I personally never use it though :)


# 4. Transitions & Animations

A `transition` is defined as the change in CSS behaviour of a DOM element when an `event` happens on it. While you may think its that same old JS stuff which Advanced CSS Selectors also help do, you should know that `transitions` help to do it in style ! So you can add the `transition-duration`, etc. `Animation` is like an enriched transition ! Here you can use the `@keyframes` rule to add CSS bahaviour at various midpoints, and not just at the start & end points.

I followed [Shay Howe's tutorial](https://learn.shayhowe.com/advanced-html-css/transitions-animations/) for both Transitions & Animations. Additionally I followed [Traversy's video](https://www.youtube.com/watch?v=zHUpx90NerM) for an illustration.

# 5. Flex Layout

If you want to layout your webpage, the flexbox module is your main toolbox ! It helps in the alignment and spacing between `items` on a `container` arranged in horizontal and vertical axes (Refer to the [MDN Flexbox Guide](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout#guides)). It works with the `display:flex` property within a `<div>` with the class of `container`. The main concepts you should strongly grasp are:  
* The two axes of flexbox
* The flex container
 
Following CSS properties to set the behaviour for children items are required within the Flex Container :

* `display: flex;` enables flexbox on the parent container.  
* `justify-content: space-between;` distributes space evenly between flex items along the main axis.  
* `align-items: center;` centers flex items vertically along the cross axis.  
* `flex-direction: column;` used in responsive layout to stack elements vertically  
* `flex-wrap: wrap;` allows flex items to wrap onto new lines when there's not enough space to fit them all on one line.  
* `flex: 1;` distributes available space among flex items. Infact `flex` is  the [shorthand property](https://developer.mozilla.org/en-US/docs/Web/CSS/Shorthand_properties) combining flex-start, flex-shrink & flex-basis.

For all these properties, I found the [Codevolution Flexbox Tutorial](https://www.youtube.com/playlist?list=PLC3y8-rFHvwg6rjbiMadCILrjh7QkvzoQ) very helpful.

_By default_, a flex item has a width which auto-adjusts to its content, and it cannot grow but can shrink, i.e. `flex: 0 1 auto`. By assigning say `flex:1` to an item, we allow it to grow when container width expands. But the `flex: 1` makes sense when compared with another item within the same container with say `flex: 2` which would grow twice as fast !

Check out my project on a Flexbox-based webpage [here](https://codepen.io/galaxyeagle/pen/LEPJQRR). 
 

# 6. Grid layout

This is the most popular alternative to the flexbox layout. Its preferred for more complex layouts. It works with the `display:grid` property within the container. The [SlayingTheDragon Tutorial on Grids](https://www.youtube.com/watch?v=EiNiSFIPIQE) explains it best.


Following properties to set the template for children items are required within the Grid Container :

* `grid-template-columns` : Eg: `grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));`  
* `grid-template-rows`  
* `grid-template-areas`  
* `gap` : Previously called `grid-gap`
 

Check out my project on a Grid-based webpage [here](https://codepen.io/galaxyeagle/pen/bNbQVwz). 

There is often a confusion whether to use flexbox or grid layout for your project. [This video](https://www.youtube.com/watch?v=3elGSZSWTbM) clarifies it. It appears that items within a flexbox container are arranged in 1D while in a grid container, they're 2D. But you should have known by now how `flex` can be made to have all 2D features. So I personally tend to use grid layout for the overall page, but flexbox for underlying sections, and that's what I did in my above project.


# 7. Header - navigation bar

This is one of the most important parts of any web-page/app. And it might test the limits of your CSS and layout skills. Let's explore.

Let's make a navbar `<nav>` with 3 parts: `brand`, `hamburger` and `menu`, contained within a `<header>` container. 



# 8. CSS-in-JS frameworks

There are several JS frameworks/libraries which can be imported in your js file that provide a unique syntax to write css. 

Examples of CSS-in-JS frameworks include `Linaria`, `JSS`, `Radium`, `Vanilla Extract`, `stitches`, `styled-components`, `emotion`, `Panda CSS` etc.


- **Approach 1**

You can import useful classes from this framework and then create an object which you can use in `className` for the element you want to style. For example consider the `Linaria` framework:

```js
// App.js

import React from 'react';
import { css } from 'linaria';

// Define your styles using Linaria's css function
const buttonStyle = css`
  background-color: blue;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;

  &:hover {
    background-color: darkblue;
  }
`;

// Create a functional component
const App = () => {
  return (
    <div>
      <h1>Welcome to Linaria Example</h1>
      <button className={buttonStyle}>Click Me</button>
    </div>
  );
};

export default App;
```

The above App.js code also shows how a js file is enough to subsume both html and css codes and a js runtime like Node can be so powerful. This is contrary to how a beginner in web dev may think of the html file to be the most important file in a project.

- **Approach 2** 

You can import a function from this JS framework and then create an custom "style" component using that function to write your vanilla css styles. Then you can export this "style" component to a parent component js file and use it there either by wrapping in a context-provider manner (eg. stitches) or by mimicking the component name (eg. styled-components), etc.

For example consider the `stitches` framework:

```js
//App.js

import { styled } from "@stitches/react";

const StyledItem = styled("div", {
  padding: "10px",
  borderBottom: "1px solid #ddd",
  "&[data-state=open]": { backgroundColor: "#f5f5f5" },
});

export function App() {
  return (
    
    <StyledItem>
        ................
    </StyledItem>
    
  );
}
```

Now consider the `styled-components` library where names such as `Button` are mimicked:

```js
import React from 'react';
import styled from 'styled-components';

const Button = styled.button`
  background-color: ${props => props.backgroundColor};
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
`;

const App = () => {
  return (
    <div>
      <Button backgroundColor="#3498db">Click me</Button>
      <Button backgroundColor="#f1c40f">Click me</Button>
    </div>
  );
};
```

In all such cases, note that these CSS-in-JS libraries allow you to replace the traditional external styles.css file with a "style" JS component which you can fill with similar vanilla css. Moreover you are styling a specific component at a time, so its a component-based scoped styling, unlike class-based frameworks which apply global styling classes. The ability to pass props to these "style" components allows dynamic styling as well.


# 9. Class-based frameworks


Note that unlike the CSS-in-JS frameworks, class-based frameworks can be directly used in HTML markup through classes, rather than being `imported` into JavaScript or React code. But additional configurations may be required: for eg. in tailwind, configuring the `tailwind.config.js` file and including tailwind in your `index.css` file.

Thus such frameworks provide a set of pre-defined classes to rapidly build custom designs directly in your HTML file. Its like someone has created classes and styled them with vanilla CSS. Now you just need to add these classes to different HTML elements, and let your app.css file be mostly empty.

Tailwind and Bootstrap are the 2 most popular class-based frameworks.

## i. Tailwind 

Tailwind uses pre-built low-level utility classes. That's why it's called a utility-first CSS framework.

Shoot to the sample project at [playcode.io](https://playcode.io/tailwind) and check out some tailwind classes used in the project. In that project, the Tailwind `CDN` is linked via a `<script>` in the index.html file. However if you're running a local project in VS Code, there are 5 ways to install tailwind in your local frontend folder as outlined in [its docs page](https://tailwindcss.com/docs/installation/using-vite) and 3 of them are mentioned below.   
*(But Tailwind keeps updating and it's best to refer to their docs for installation instructions. For example in April 2025, Tailwind v4 was reieased. The biggest change in Tailwind 4 is they have moved to to a CSS-first-configuration — Whcih means the tailwind.config.js has been removed and all configuration now happens directly in the main CSS file.)*

- **PostCSS + CLI**

The most "universal" approach is installing wth PostCSS+CLI. Here Tailwind is installed as a PostCSS plugin and configured with the Tailwind CLI.


- **Tailwind CLI Alone (No PostCSS Plugin)**

This approach uses the Tailwind CLI to generate a standalone CSS file without relying on PostCSS as a processor.

- **Tailwind as a Vite Plugin**

Tailwind is processed by Vite automatically, no extra build scripts are needed. Vite’s HMR updates styles instantly without a separate --watch command. I prefer this approach for my Vite project. The setup is as follows:


If you need a video tutorial on this, you can follow [this video](https://www.youtube.com/watch?v=SPr-1cwVn1k) for including Tailwind in a normal web project and [this video](https://www.youtube.com/watch?v=L3wJe66tlBk) for including in a React project.

## ii. Bootstrap

Unlike Tailwind, Bootstrap uses more broad component-based classes. The following illustration to style a button illustrates it best:

```html
<!--Tailwind -->

<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Click me
</button>
```

```html
<!--Bootstrap -->

<button class="btn btn-primary">
  Click me
</button>
```

So Bootstrap tends to style the entire button as a component.

In short,
```  
Tailwind: 90% utility classes, 10% component classes  
Bootstrap: 10% utility classes, 90% component classes
```
Note that
```
Vanilla CSS, CSS-in-JS frameworks and Class-based frameworks constitute the CSS FRAMEWORKS you can use to style your app. 
```