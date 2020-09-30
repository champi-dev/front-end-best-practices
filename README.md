# Best practices for Front-end Web Development
## CSS
### BEM
#### Block element modifier a concept for writing maintainable css.

Writing css may be a burden for some developers. Complex designs require large/maintainable code that can be easily modified.

That's what BEM is for. BEM stands for **block, element, modifier** and the concept is to always write our styles thinking about blocks.

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

A block is a meaningful chunk of markup that has one or more children elements (in the above example both the span and img elements are button's children).

An element is every single block's children.

What about modifiers?

What if the above submit button also needed a `disabled` class that changes the button's background color to grey?

E.g. Vuejs
```html
	<button :class="{
		'button': true,
		'button--disabled': disabled
	}">
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

Design systems are unified concepts that ensure **consistency**.

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
	small: 50em, 
	medium: 76.8em, 
	large: 102.4em, 
	larger: 128em, 
	extra-large: 144em
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

`rem` is a relative unit of measurement, it means that its value directly depends on a relative (root) value (commonly set with a `font-size` targeting the `html` tag). If that root value changes, the value expressed in `rem` will change uniformly.

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

If we now change html's `font-size` to `8px`, everything setted with `rem` will now reevaluate to a `20%` less and therefore will look *smaller*.

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
More often than not you'll find yourself using `flexbox` everytime you're setting dimensions with percentages.

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
We usually have 3 mayor breakpoints (mobile, tablet and desktop) but this *does not* mean that these are the only breakpoints you should use. Start with those mayor breakpoints but then resize the screen and watch for broken/inconsistent elements, I guarantee that you'll find many resolutions where the adjustments that you already have in place will look just **ugly**.

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
#### You don't know js 
https://github.com/getify/You-Dont-Know-JS
This is a series of books that will show you the ins and outs of `javascript`. If you are serious about js development you must read all of those books.

#### Clean code (for js) 
https://github.com/ryanmcdermott/clean-code-javascript
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
#### Style guide 
https://vuejs.org/v2/style-guide/ Be sure to read the official Vuejs style guide. Following the advices given there will give consistency to the codebase and with the usage of a formatter it'll look even more standardized.

#### State management 
When an application grows and starts becoming complex we find ourselves in a situation where we need to be careful with the way we organize our code.

The state management pattern aids in giving us clarity about our data flow. Use this pattern when your app has *global* data that many components may share.

Don't use this pattern for data generated by the ui (state inside your components) but rather use it for managing data that comes from the *server*.


##### Vuex 
https://vuex.vuejs.org/ Vuex docs are crystal clear and you should take the time to read them and understand how to use it properly.
Here's some advice for organizing the `store` folder.

###### Build a module for every meaningful concept of the data you're handling (tigthly coupled with the api services)

Let's imagine that we're building the `store` folder for an ecommerce app.
We'll need the following modules:
- auth (for handling user authentication)
- products (for handling the ecommerce products)
- cart (for handling the checkout)

Notes: 
- Assume that every api call will return `[ res ]` if 'success' and `[ null, error ]` otherwise*
I'll expand on this in the `App architecture` section.

- Actions execute api calls and commit mutations as needed.

- Actions return `{ status }` for it's usage in the component that called the action (this is useful for toggling styles upon success/error).

`auth.js`
```javascript
import POSTauth from 'apiCalls/POSTauth'

export default {
	state: {
		token: '',
		userName: ''
	},

  mutations: {
  	setToken(state, token) {
  		state.token = token
  	},
  	setUserName(state, userName) {
  		state.userName = userName
  	}
  },

  actions: {
  	async loginUser({ commit }, formData) {      
  		const [res, error] = await POSTauth(formData)

  		if (error) {
  			return { status: 'error' }
  		} else {
  			commit('setToken', res.token)
  			commit('setUserName', res.userName)
  			return { status: 'success' }
  		}
    },
  },

  getters: {
  	isAuth(state) {
  		return Boolean(state.token)
  	}
  }
}
```

`products.js`
```javascript
import GETproducts from 'apiCalls/GETproducts'

export default {
	state: {
		products: []
	},

  mutations: {
  	setProducts(state, products) {
  		state.products = products
  	}
  },

  actions: {
  	async getProducts({ commit }) {      
  		const [res, error] = await GETproducts()

  		if (error) {
  			return { status: 'error' }
  		} else {
  			commit('setProducts', res.products)  			
  			return { status: 'success' }
  		}
    },
  }
}
```

`cart.js`
```javascript
import POSTprocess_payment from 'apiCalls/POSTprocess_payment'

export default {
	state: {
		productsInBasket: []
	},

  mutations: {
  	handleProduct(state, { action, selectedProduct }) {
  		const addProduct = () => 
  			[...state.productsInBasket, selectedProduct]

  		const deleteProduct = () => 
  			state.productsInBasket.filter(prd => prd.id !== selectedProduct.id)

  		state.productsInBasket = action === 'add' ? addProduct() : deleteProduct()
  	}
  },

  actions: {
  	async processPayment({ state }) {      
  		const [res, error] = await POSTprocess_payment(state.productsInBasket)

  		if (error) {
  			return { status: 'error' }
  		} else {  			
  			return { status: 'success' }
  		}
    },
  },

  getters: {
  	totalPayment(state) {
  		return state.productsInBasket.reduce((a, b) => a.price + b.price)
  	}
  }
}
```


`store.js`
```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import auth from './auth'
import products from './products'
import cart from './cart'

Vue.use(Vuex)

const store = new Vuex.Store({
	modules: {
		auth,
		products,
		cart
	}  
})

export default store
```

#### Complex component state
There are cases where handling state with props or bus events can quickly become cumbersome. I'm talking about components that are complex by design/nature and therefore require careful thought in it's construction.

In case you're still not following let me ask you this question:
Have you ever written a component that passes down many props to a middle component that also passes down those props? **(this is known as prop drilling)**

If you answered **yes** keep reading, **otherwise** you may not need the pattern I'm about to describe.

##### Provide/Inject
https://vuejs.org/v2/api/#provide-inject This feature is similar to React's Context. It lets you define data in a parent component and make it available to all of its children *no matter how deep the component hierarchy is* without needing to manually pass it down.

With this feature available in our toolbelt we can now replicate a *state management pattern* for our complex components (consequently gaining clarity over data flow).

Let's build a **signup** form with two inputs (email and password) and a submit button.
- Each input should have it's on validation
- If an input is invalid it should apply an *error style* and also display and *error message*
- If the form is invalid the submit button should be *disabled*

[Go to live demo](https://codesandbox.io/s/provide-inject-72mne)

#### App architecture
A key aspect of writing maintable apps is to conceptualize it as many layers, each one with it's own responsibility, and together they form the bulk of the app.

Some people have referred to this as **separation of concerns** but the term hasn't been clarified enough to prevent developers into thinking that having separate files for the `html` - `css` - `js` is separating concerns. Nope, that's not it, that's just separation of files.

**separation of concerns** is about defining responsability.

##### pages - components - networkLayer
I've come up with this pattern of having the following folders in my projects:

- pages: Here are the files that are responsible for rendering an entire page to the screen (it may be also called views). These ones make calls to the store.

- components: Here are the files that represent a unit to be used inside a Page.

- networkLayer:
	This is the layer that connects to the *backend* and manages the data flow.
	![networkLayer](https://i.ibb.co/YWxLT2M/Screen-Shot-2019-06-20-at-2-04-13-AM.png)	

	- apiCalls: Here are all the apiCalls that can be made by the app. These ones are called within a store module action. These return either `[res]` or `[null, error]`. The idea here is to separate the concepts of **making a server request** and **managing the requested data**.

	- store: Here is the state management setup to be used. A config file and a `modules/` folder containing the store modules.	

E.g `GETproducts.js` (an api call)
```javascript
import axios from 'axios'
export const GETproducts = () =>
  axios
    .get(
      '/products/'
    )
    .then((res) => [ res.data ])
    .catch((error) => [ null, error ])
```
