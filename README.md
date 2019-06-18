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
	primary: blue;
	secondary: lightblue;
);

$font: (
	main-family: ('Inter UI', sans-serif),	
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
	nav-bar-height: 6rem
);
```

### Responsive design
#### about pixel perfect  
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


