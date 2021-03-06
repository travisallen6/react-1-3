<img src="https://s3.amazonaws.com/devmountain/readme-logo.png" width="250" align="right">

# Project Summary

<p>In this project we will create an e-commerce React application from start to finish. </p>

This project is a continuation from previous projects. If you are wanting to do this project then start with <a href="https://github.com/DevMountain/react-1-1"> this repo</a> first.

# Day 3

Today we will be focusing on making HTTP requests using the Axios library. We will gain some experience working with third-party API's, by retrieving an API key and using it to validate our HTTP requests. After completing this project, you should have a better understanding of the following concepts:

- Axios
- HTTP Requests
- React Lifecycle Methods

# Live Example

<img src="readme-assets/screenshot.png" alt="screenshot"/>

<a href="https://devmountain.github.io/react-1-3/">Click Me!</a>

## Setup

- For this project you will be continuing the Day 2 project.
- You should have completed Day 2 Part 2 in order to begin today's project.

# Part 1

### Summary

In this part we will use `axios` in the `componentDidMount` lifecycle method of `App.js`, to get a list of products from the API. We will refer to the documentation for this API, to tell us how to format our axios requests. We will also be able to add new items to the products API server. In order to access the server, we will have to request an `API key`. This is a common practice for public API's; requiring users to provide a personal API key so that they can validate, control and track who has access to their servers. In the case of our products API, this API key needs to be applied to all http requests as a query parameter called `key`.

## Step 1

- Run `npm install axios` in your project root directory, to install the `axios` package.
- Import `axios` in `App.js`, at the top of the file, like so: `import axios from "axios";`
- We will be using products from the API instead of the local ones, so delete the products you hard coded into your `App.js`
- Create a `componentDidMount` method on the `App` component.
- In the `componentDidMount`, we will make an axios request to get a list of products from the server. Refer to the API docs for how to format your axios request. Products API docs: `http://104.248.178.153/products/`
- In order to use the API, an `API key` is required. Use `Postman` to request an `API key` from the products server.

  - This will be a `GET` request. The API docs will tell you how to format the request.
  - Once you have a key, store it on state in your app

<details><summary> Detailed Instructions </summary>

Let's begin by opening `src/App.js` and creating a `componentDidMount` method after the `constructor` but before the `render` method.

```js
constructor(){
    props()
    this.state = {
        apiKey: '<<API key that you got using postman>>'
    }
}
componentDidMount() {
    // axios request will go here
}
```
</details>

## Step 2

- Now we need to get the list of products from the API, using the API key that we have just acquired.

    <details> <summary> example code </summary>

    ```js
    componentDidMount() {
      axios.get(`http://104.248.178.153/products/catalog?key=${this.state.apiKey}`)
        .then( productsResponse => {
            // ...do something with the response
        })
    }
    ```

    </details>

- The products API will give us a list of items. In order to keep track of the quantity of an item in the cart, each item needs to have a `quantity` value initialized to 0.

  - Use a `forEach` or for loop, to add a `quantity` value, which defaults to 0, to each item returned from the API.

    <details> <summary> example code </summary>

    ```js
    axios
      .get(`http://104.248.178.153/products/catalog?key=${this.state.apiKey}`)
      .then(productsResponse => {
        productsResponse.data.forEach(item => (item.quantity = 0));
        // ...
      });
    ```

    </details>

- The product API will give us data sorted by category of `hat`, `shirt`, `swag`, `cold weather`. We need to sort this response data, and put it on matching objects on state.

  - In order to do this, we can filter the result object by the item category.

    <details> <summary> example code </summary>

    ```js
    let hat = productsResponse.data.filter( item => item.category === "hat");
    let shirt = productsResponse.data.filter( item => item.category === "shirt");
    let swag = productsResponse.data.filter(item => item.category === "swag");
    let coldWeather = productsResponse.data.filter(item => item.category === "cold weather");
    // now that we have this data sorted into categories, we can set these on state
    this.setState({
      hat: hat,
      shirt: shirt,
      swag: swag,
      coldWeather: coldWeather
    });
    ```

    </details>

### Solution

<details> 
<summary> <code> App.js </code> </summary>

Modify componentDidMount, to also hit the products endpoint, to get all products (according to the [API documentation](http://104.248.178.153/products/)).

```js
componentDidMount() {
  axios.get(`http://104.248.178.153/products/catalog?key=${this.state.apiKey}`)
    .then( productsResponse => {
      // we also need to add a quantity to the each product item
      productsResponse.data.forEach( item => item.quantity = 0 )
      // here we need to sort the resulting product data by category
      let hat = productsResponse.data.filter( item => item.category === "hat" )
        let shirt = productsResponse.data.filter( item => item.category === "shirt" )
        let swag = productsResponse.data.filter( item => item.category === "swag" )
        let coldWeather = productsResponse.data.filter( item => item.category === "cold weather" )
      // now that we have this data sorted into categories, we can set these on state
      this.setState({
        hat: hat,
        shirt: shirt,
        swag: swag,
        coldWeather: coldWeather
      })
    })
  })
}
```

</details>

## Step 3

- Create a file in `src/components/` called `AddProduct.js`.
- This component should be able to store the following data on state: `name`, `description`, `price` and `image`. These will be strings, and can be initialized to empty strings.
- Create a text input field for each of these state values. The text input should be able to update its value on state.

  - In order to do this, for the name input field, we can create a method called `handleName`, which will accept a string value, and call `setState` to update the `name` value on state.

    <details> <summary> example code </summary>

    ```js
    handleName(newName) {
      this.setState({ name: newName })
    }
    ```

    ```js
    <input type="text" value={this.state.name} onChange={ (event) => this.handleName(event.target.value) } />
    ```

    </details>

  - The name input field should have a `value` attribute set to `this.state.name`, so that it always accurately displays the corresponding value on state.
  - Do this for all four input fields, creating a method for each of them, like `handleName`, `handleDescription`, `handlePrice`, `handleImage`.

- Create a `submit` button. This button should submit the data for the new item, by calling a function which will be passed from the `App` component as a prop in the next step.

  <details> <summary> example code </summary>

  ```js
  // on AddProduct.js
  submit() {
    const newItem = {
      name: this.state.name,
      description: this.state.description,
      price: this.state.price,
      image: this.state.image
    }
    this.props.submit(newItem)
  }
  ```

  ```js
  <button onClick={ () => this.submit() }> submit </button>
  ```

  </details>

- Make sure to label your input fields with a `label` tag, so that the user knows which field he is updating.

### Solution

<details>
<summary> <code> AddProduct.js </code> </summary>

```js
import React, { Component } from "react";

export default class AddProduct extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "",
      description: "",
      price: "",
      image: ""
    };
  }

  handleName(name) {
    this.setState({
      name: name
    });
  }

  handleDescription(description) {
    this.setState({
      description: description
    });
  }

  handlePrice(price) {
    this.setState({
      price: price
    });
  }

  handleImage(image) {
    this.setState({
      image: image
    });
  }

  submit() {
    const newItem = {
      name: this.state.name,
      description: this.state.description,
      price: this.state.price,
      image: this.state.image
    };
    this.props.submit(newItem);
  }

  render() {
    const { nameInput, descriptionInput, priceInput, imageInput } = this.state;
    return (
      <div>
        <label>Name: </label>
        <input
          onChange={e => this.handleName(e.target.value)}
          value={nameInput}
        />
        <label>Description: </label>
        <input
          onChange={e => this.handleDescription(e.target.value)}
          value={descriptionInput}
        />
        <label>Price: </label>
        <input
          onChange={e => this.handlePrice(e.target.value)}
          value={priceInput}
        />
        <label>Image: </label>
        <input
          onChange={e => this.handleImage(e.target.value)}
          value={imageInput}
        />
        <button onClick={() => this.props.submit()}> Submit </button>
      </div>
    );
  }
}
```

</details>

## Step 4

- Create a `submit` method in `App.js`.
- This method should accept one argument, an `object` with a name, description, price and image value.
- This method should submit a new product to the products API, using an axios `POST` request. Refer to the [API docs](http://104.248.178.153/products/) for how you should format this request.
  - Remember to append your API key onto the request URL as a URL query parameter.
  - e.g. `"/api/product?key=${this.state.apiKey}`
- The resulting data from the post request will be a complete list of products, including the newly added item; use this result data to update the products on state.
  - To filter the products and put them on state, you can refer to the code in `componentDidMount`, where we first got the list of products.

### Solution

<details>
<summary> <code> src/App.js </code> </summary>

```js
submit ( product ) {
  axios.post(`http://104.248.178.153/products/catalog?key=${this.state.apiKey}`, product)
    .then( response => {
        // filter response data by category
      let hat = productsResponse.data.filter( item => item.category === "hat" )
      let shirt = productsResponse.data.filter( item => item.category === "shirt" )
      let swag = productsResponse.data.filter( item => item.category === "swag" )
      let coldWeather = productsResponse.data.filter( item => item.category === "cold weather" )
      // set these categories on state
      this.setState({
        hat: hat,
        shirt: shirt,
        swag: swag,
        coldWeather: coldWeather
      })
    })
}
```

</details>

## Step 5

- Import `AddProduct.js` into `App.js`.
  - Use `<AddProduct />` in the render method of `App`, and pass it the prop `submit` being equal to `this.submit`. e.g. `<AddProduct submit={ this.submit } />`
- Bind the `submit` method on App in the constructor function, so that it will have the right `this` context (`this` needs to refer to the App component).

### Solution

<details> 
<summary> <code> App.js </code> </summary>

```js
import AddProduct from "./components/AddProduct";

export default class App extends Component {
  constructor() {
    super();

    this.state = {
      /* ... */
    };

    this.submit = this.submit.bind(this);
  }

  submit() {
    // ...
  }

  render() {
    return (
      <div className="app-component">
        <AddProduct submit={this.submit} />
        {/* ... */}
      </div>
    );
  }
}
```

</details>

# Part 2


### Summary

In this part we will be changing up the way we handle the cart. All of the cart will be stored on the API server, and we will have to use HTTP requests to add or remove items from the cart.

## Step 1

- The `addToCart` method on `App.js` should be refactored to make an axios `POST` to the appropriate endpoint (refer to the [API docs](http://104.248.178.153/products/) for formatting the URL).
  - `addToCart` should take one argument, an item Id, which will be used in the axios call.
  - Chain a `.then` onto axios post request.
  - In the `.then` callback function, set the response data to the `cart` value on state.

### Solution

<details>
<summary> <code> App.js </code> </summary>

```js
addToCart( item ) {
  axios.post(`http://104.248.178.153/products/catalog/${item}?key=${this.state.apiKey}`)
    .then(cartResponse => {
      this.setState({ cart: cartResponse.data })
    })
}
```

</details>

## Step 2

- The `removeFromCart` method should be refactored to make an axios request.
- Refer to the [API docs](http://104.248.178.153/products/) on which endpoint to hit.
- Chain a `.then` onto the request to set the resulting data onto the `cart` value on state.

### Solution

<details>
<summary> <code> App.js </code> </summary>

```js
removeFromCart( itemId ) {
  axios.delete(`http://104.248.178.153/products/catalog/${itemId}?key=${this.state.apiKey}`)
    .then( cartResponse => this.setState({ cart: cartResponse.data }))
}
```

</details>

# Part 3

### Summary

In this part we will create a reusable button component, and then replace all of the buttons in our app with this component. The purpose of this exercise is to give you practice using a small, reusable component.

## Step 1

- Create a `Button.js` file.
- In this file, create a functional component as the default export.
  - A functional component still uses `import React from 'react'`, but does not import `{ Component }`.
- Also `import PropTypes from "prop-types"`
- Create a propTypes object for this component.
  - This component should expect two values on props: a function called `handleClick` and a string called `text`.

### Solution

<details>
<summary> <code> Button.js </code></summary>

```js
import React from 'react';
import PropTypes from 'prop-types';

function Button (props) {
  return (
    // empty for now
  )
}

Button.propTypes = {
  handleClick: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired
}
```

</details>

## Step 2

- Now create the JSX for the Button component. It should return a button element.
- The `onClick` for this element should be the function passed in on props.
- It should display the text passed in on props.

### Solution

<details> <code> Button.js </code> </button>

```js
function Button(props) {
  return <button onClick={props.handleClick}> {props.text} </button>;
}
```

</details>

## Step 3

- Now go through your application and where ever you are using regular `button` elements, replace them with our new, custom `Button` component. Remember that this custom component takes the `text` string as a prop, as well as a function on `handleClick`.

# Part 4 - Black Diamond

### Summary

In this part we will be creating a search function to query the API with a name string and get specific results. If you created the search function as part of Day 2 Black Diamond, you will need to refactor it to use the API. We will also be using a [payment API](http://104.248.178.153/payment/) to process user payments.

## Step 1

- Create an `input` box on App of type `text`, that will take in user input in order to filter the list of products from the server.
- Create a value on state called `searchInput`. This will represent the value that the user has typed into our search input box.
  - Give the search input box a `value` attribute, equal to the `searchInput` value on state. (`this.state.searchInput`)
- Add a button under the search input box that will activate the `handleSearch` method on click.
- We will write the `handleSearch` function in the next step.

### Solution

<details> 
<summary> <code> App.js </code> </summary>

```js
<input type="text" />

<Button
  handleClick={this.handleSearch}
  text={"search"}
  value={this.state.searchInput}
/>
```

</details>

## Step 2

- Create a `handleSearch` method on App.
- It should make a get request to the API for the products with the `query parameter` of the user `search input`.
- Filter the results onto category arrays to be stored on state, just as we have done when we get the products list in `componentDidMount`

### Solution

<details> 
<summary> <code> App.js </code> </summary>

```js
handleSearch() {
  axios.get(`http://104.248.178.153/products/catalog?key=${this.state.apiKey}&name=${this.state.searchInput}`)
    .then(productsResponse => {
      // filter results onto arrays
      let hat = productsResponse.data.filter(item => item.category === "hat");
      let shirt = productsResponse.data.filter(item => item.category === "shirt");
      let swap = productsResponse.data.filter(item => item.category === "swap");
      let coldWeather = productsResponse.data.filter(item => item.category === "cold Weather");
      this.setState({
        hat: hat
        shirt: shirt
        swap: swap
        coldWeather: coldWeather
      });
    }
}
```

</details>

## Step 3

- Now we are going to use another API to process the user's payment when they checkout.
  - Payment API docs: http://104.248.178.153/payment/
- In order to use the Payment API, we will need to request a separate api key.

  - Do this using `Postman`, and store the value on App's state manually

    <details><summary> Detailed  Instructions </summary>

    ```js
    constructor(){
      super()

      this.state = {
        paymentApiKey = '<<key you get from making get request with postman for payment api key>>'
        //...
      }
    }
    ```

    </details>

## Step 4

- Then, in our checkout method, we will use an axios post to send the cart to the payment API.
- Create a `POST` request in the `checkout` method, that will send the `this.state.cart` array.

### Solution

<details> 
<summary> <code> App.js </code> </summary>

```js
checkout() {
  if (!this.state.addressInput || !this.state.ccInput){
    alert("Fill out required fields first.");
  } else if (this.state.cart.length===0) {
    alert("Cart is empty.");
  } else {
    axios.post(`http://104.248.178.153/products/catalog?key=${this.state.paymentAPIKey}`, this.state.cart)
    .then(() => {
      axios.delete(`http://104.248.178.153/products/cart/checkout?key=${this.state.apiKey}`)
        .then(checkoutResponse => {
          this.setState({
            cart: checkoutResponse.data,
            addressInput: "",
            ccInput: ""
          })
      })
    })
  }
}
```

</details>
