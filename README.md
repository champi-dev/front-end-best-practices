# Best practices for Front-end Web Development

## CSS
### BEM
#### Block element modifier a concept for writing maintainable css.

Writing css may be a burden for some developers. Complex designs require large/maintanable code that can be easily modified.

Thats what BEM is for. BEM stands for *block, element, modifier* and the concept is to always write our styles thinking about blocks.

E.g.
```html
	<button class='button'>
		<span class='button__text'>Submit<span>
		<img class='button__icon' src='icon.svg' alt='' />
	</button>
```

```css
	.button {
		display: flex;
		align-items: center;
		background: lightgreen;
		padding: 0 1.6rem;
	}

	.button__text {
		font-size: 2rem;
	}

	.button__icon {
		width: 1.4rem;
		height: 1.4rem;
	}
```

The block is a meaningful chunk of markup that has one or more children elements (In the above example both the span and img elements are button's children).

The element is every single block's children.

What about the modifiers?

What if the above submit button also needed a `disabled` class that changes the button's background color to grey?

E.g. Vuejs
```html
	<button :class=`
		button 
		${disableBtn ? 'button--disabled' : ''}
	`>
		<span class='button__text'>Submit<span>
		<img class='button__icon' src='icon.svg' alt='' />
	</button>
```

```css
	.button--disabled {
		background: lightgrey;
	}
```

### Sass
#### Using colors, fonts, sizes and breakpoints.

Design systems are unified concepts that ensure *consistency*.

Front-end developers must make the effort of understanding those design systems and look for repetitive patterns **before** writing any line of code.

##### map-get

The best way to achieving consistency is having a **single source of truth**. In sass we have a utility called `map-get` that makes that really easy.

E.g `variables.scss`
```scss
$colors: (
	blue: #4286f4;
	lightblue: #b8d1f9;
);

$font: (
	main-family: sans-serif,	
	weight-thin: 300,
	weight-medium: 500,
	weight-fat: 700,
	base-size: 62.5%,
	smaller-size: 50%
);

$breakpoints: (
	small: 500px, 
	medium: 768px, 
	large: 1024px, 
	larger: 1280px, 
	extra-large: 1440px
);

$sizes: (
	header-height: 6rem
);
```

E.g `App.vue`
```html
<template>
  <div class='app'>
    <div class='app__header'>
    	<span class='header__text'>Hello World</span>
    </div>
  </div>
</template>

// use the scoped atrr in components
<style lang="scss">
  @import "./variables";

  html {
  	font-size: map-get($font, base-size);
    font-family: map-get($font, main-family);
    font-weight: map-get($font, weight-thin);
    box-sizing: border-box;

    @media screen and (max-width: map-get($breakpoints, small)) {
    	font-size: map-get($font, smaller-size);
    }
  }

  .app {
  	display: flex;
  	flex-direction: column;
  	background: map-get($colors, blue);

  	&__header {
  		width: 100%;
  		height: map-get($sizes, header-height);
  		background: transparent;
  		display: flex;
  		justify-content: center;
  		align-items: center;

  		.header__text {
  			color: map-get($colors, lightblue);
  		}
  	}
  }
</style>
```

Colors, fonts, sizes and breakpoints must be defined in `variables.scss` and used when needed. You should avoid using values (for colors, fonts, sizes and breakpoints) that are not already defined in `variables.scss`. If you need to create a new value (maybe the designer added a new color), add it to `variables.scss` and then use it with `map-get`.

### Responsive design
#### about pixel perfect  
#### about media queries https://zellwk.com/blog/media-query-units/
#### rem vs px
#### when to use %


## JS
### Recommended reading
#### Clean code (for js) https://github.com/ryanmcdermott/clean-code-javascript
#### You don't know js https://github.com/getify/You-Dont-Know-JS

### Tooling
#### Linter: Eslint
#### Formatter: Prettier/Standard
#### Bundler: Webpack

### Vue.js
#### Style guide: https://vuejs.org/v2/style-guide/
#### State management: Vuex
#### Complex component state: Provide/Inject
#### App architecture: Pages - Components - NetworkLayer


