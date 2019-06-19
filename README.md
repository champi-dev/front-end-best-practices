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
#### About pixel perfect  

Designers and developers share the goal of bringing a product to life, yet they use different tools for achieving it.

When a designer hands off a mockup he/she expects that the developer takes care of converting that mockup to a live web version. Often times what he/she forgets is that *the web* is a dynamic platform that changes it's width/height according to the user's device and therefore the design *must adapt* to this reality.

##### A practical view on pixel perfect web design

Mobile, tablet and desktop. Those are the most common screen resolutions that a website is designed for.
- Developers should strive to get as close to the mockups in those resolutions as possible and make sure that it still looks good in uncommon resolutions (resize the browser both horizontally and vertically, everything should look consistent and in place).
- Designers should help developers fix those situations when an uncommon resolution brakes the design.


#### Stop using `px`, use `rem` instead

`px` is a fixed unit of measurement. Does *fixed* sound responsive to you?
No? Why are you still using it?

`rem` is a relative unit of measument, it means that its value directly depens on a relative (root) value (commonly set with a `font-size` targeting the `html` tag). If that root value changes, the value expressed in `rem` will change uniformly.

It's a good practice to set the html's `font-size` to `10px`. This way it'll be easier to write your css thinking in `px` (as you're used to) but using `rem` for everything else.

E.g. 
```css
html {
	font-size: 10px;
}

button {
	font-size: 1rem; // equals 10px
}

span {
	font-size: 1.6rem; // equals 16px
	width: 20rem; // equals 200px
	height: 14rem; // equals 140px
}
```

##### What's the benefit?

If we now change html's `font-size` to `8px`, everything that was setted with `rem` will now reevaluate to a `20%` less and therefore will look *smaller*.

E.g. 
```css
html {
	font-size: 8px;
}

button {
	font-size: 1rem; // now equals 8px
}

span {
	font-size: 1.6rem; // now equals 12.8px
	width: 20rem; // now equals 160px
	height: 14rem; // now equals 112px
}
```

#### When to use %

Percentage units come in handy when you need to set the dimensions of an element's child (width/height).
More often that not you'll find yourself using `flexbox` everytime you're setting dimensions with percentages.

E.g. `Navbar.vue`
```html
<template>
  <nav class='navbar'>
  	<a class='navbar__link' href="#pricing">Pricing</a>
  	<a class='navbar__link' href="#ourwork">Ourwork</a>
  	<a class='navbar__link' href="#about">About</a>
  	<a class='navbar__link' href="#legal">Legal</a>
  </nav>
</template>

<style lang="scss" scoped>
// Whe may want to give the first 3 links more importance/space
.navbar {
	display: flex;

	// Setting the width with percentage will keep the links space distribution as we
	intended even when the screen width changes
	&__link {
		width: 30%;

		&:last-child {
			width: 10%;
		}
	}
}
</style>
```

#### Writing media queries 
##### Use `em` in your media queries
In this link https://zellwk.com/blog/media-query-units/ you'll see an experiment writing media queries using `px`, `rem` and `em`. The only unit that achieved consistency across browsers was the `em` unit. Give that link a quick read and then come back.

##### Write as many media queries as you see fit
We usually have 3 mayor breakpoints (mobile, tablet, desktop) but this *does not* mean that these are the only breakpoints you should use. Start with those mayor breakpoints but then resize the screen and watch for broken/unconsistent elements, I guarantee that you'll find many resolutions where the adjustments that you already have in place will look just *ugly*.

Write your media queries inside rules, don't write a single media query for everything. This way you won't end up maintaning 2 code structures and you'll also be able to quickly understand how an element will change upon resolution changes.

E.g.
Code this
```scss
.container {
	display: flex;

	&__block {
		width: 80%;
		margin: 0 auto;
		padding: 0 2.4rem;

		@media screen and (max-width: 40em) {
			width: 100%;
			margin: unset;
		}

		@media screen and (max-width: 30em) {
			padding: 0 1.6rem;
		}

		.block__text {
			font-size: 1.6rem;

			@media screen and (max-width: 40em) {
				font-size: 1.4rem;
			}

			@media screen and (max-width: 30em) {
				font-size: 1.2rem;
			}
		}
	}
}
```

Not this
```scss
.container {
	display: flex;

	&__block {
		width: 80%;
		margin: 0 auto;
		padding: 0 2.4rem;

		.block__text {
			font-size: 1.6rem;
		}
	}
}

@media screen and (max-width: 40em) {
	.container {
		&__block {
			width: 100%;
			margin: unset;

			.block__text {
				font-size: 1.4rem;
			}
		}
	}
}

@media screen and (max-width: 30em) {
	.container {
		&__block {
			padding: 0 1.6rem;

			.block__text {
				font-size: 1.2rem;
			}
		}
	}	
}
```

## JS
### Recommended reading
#### You don't know js https://github.com/getify/You-Dont-Know-JS
This is a series of books that will show you the ins and outs of `javascript`. If you are serious about js development you must read all of those books.

#### Clean code (for js) https://github.com/ryanmcdermott/clean-code-javascript
Taken from Uncle Bob's original *Clean code* book I present to you the concepts/rules that will make your code more readable (for your future self or your colleagues).

### Tooling
Modern Front-end development uses modern tools that make the development experience pretty smooth.
Nowadays almost every font-end project will use a *linter*, *formatter/format-rules* and a *bundler*.

The industry standards as of 2019 are:
- Linter: Eslint
- Formatter: Prettier (though I prefer Standard)
- Bundler: Webpack

If you're using the `vue-cli` you don't need to worry about configuring these, just make sure that you manually select these options when creating a project:
- Babel
- CSS Pre-processors --> Sass/SCSS (with node-sass)
- Linter / Formatter --> ESLint + Standard config --> Lint and fix on commit

### Vue.js
#### Style guide https://vuejs.org/v2/style-guide/
Be sure to read the official Vuejs style guide. Following the advices given there will give consistency to the codebase and with the usage of a formatter (I recommend Standard) it'll look even more standardized.

#### State management: Vuex
#### Complex component state: Provide/Inject
#### App architecture: Pages - Components - NetworkLayer


