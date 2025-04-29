---
# You can also start simply with 'default'
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: /assets/intro.jpg
# some information about your slides (markdown enabled)
title: Software Development | Foundations
info: |
  ## Software Development | Foundations
# apply unocss classes to the current slide
class: text-left
drawings:
  persist: false
transition: slide-left
mdc: true
---

# Express App Refactor
Back-End Development - part 9/12
- [ ] Reorganize how our Express app is structured
- [ ] Schema types and Validation
- [ ] Querying

<div class="abs-br m-6 text-xl">
  <a href="https://github.com/slidevjs/slidev" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
-->

---
transition: slide-left
---

# Recap & Refactor (pg.1)
(50 min) Express Setup Instructions v.2

1. Create a new folder and go into it via cd folder-name, `npm init -y`
1. `npm i express mongoose morgan dotenv cors`, `npm i --save-dev nodemon`
1. create src/server.js file with the following code:

```js
import express from "express";
import cors from "cors";
import morgan from "morgan";

export const app = express(); // to be imported in index.js

app.use(cors());
app.use(morgan("dev"));
app.use(express.json()); // no longer need body-parser; not needed after Express v4.16
app.use(express.urlencoded({ extended: true }));

app.get("/", (req, res) => {
   res.send("🚚 Welcome to the Food Truck!");
});
```


<!--
-->

---
transition: slide-left
---

# Recap & Refactor (pg.2)

1. Create `/src/connect.js`

```js
import mongoose from "mongoose";

export const connect = (url) => mongoose.connect(url); // to be imported in index.js
```

---
transition: slide-left
---

# Recap & Refactor (pg.3)
1. Move any hard coded values to .env file (ex: PORT=3000)
1. Create src/index.js:
    ```js
    import dotenv from "dotenv";
    dotenv.config();
    import { connect } from "./connect.js"; // will connect to mongoDB
    import { app } from "./server.js";
    
    app.listen(process.env.PORT, () => {
      console.log(`Server running on http://localhost:${process.env.PORT}`);
    });
    ```
1. Since we are using the import way (aka ES Module way), we have to edit package.json to have "type": "module",
1. Edit package.json so you can use nodemon. Inside "scripts" block enter new line:
    ```js
    "start": "nodemon ./src/index.js"
    ```
1. In terminal: `npm run start`

---
transition: slide-left
---

# Recap & Refactor: Routes (pg.4)

1. in /src folder, create router.js:
    ```js
    import { Router } from "express";
    import orderController from "./controllers/orderController.js"; // will direct traffic

    export const router = Router(); 

    // Order
    router.get("/order", orderController.getOrders);

    // TODO: Fill in rest of CRUD routes
    ```
    - in server.js, import your above router file `import { router } from "./router.js"`
    - then after your `app.get('/') statement, place the following middleware:
    ```js
    app.use('/api', router)
    ```
1. Next let's developer our orderController.getOrders function

<!--
-->

---
transition: slide-left
---

# Recap & Refactor: Controller (pg.5)

1. Create `/src/controllers/orderController.js`
    ```js
    import orderHandler from "../handlers/order.js";

    const getOrders = async (req, res) => {
      const orders = await orderHandler.getAllOrders();  // will call our models
      res.status(200).json(orders);
    };

    // TODO: implement rest of CRUD controllers

    export default {
      getOrders,
      // TODO: export rest of CRUD controllers
    };
    ```

---
transition: slide-left
---

# Recap & Review: Handlers (pg.6)

1. Create `src/handlers/order.js`
  ```js
  import Order from "../models/order.js";  // will handle DB interactions

  let receiptNo = 1;

  // Get all
  const getAllOrders = async () => {
    return await Order.find().lean();
  };

  // TODO: implement rest of CRUD handlers

  export default {
    getAllOrders,  // TODO: export rest of CRUD handlers
  };
  ```

---
transition: slide-left
---

# Recap & Review: Schemas and Models (pg.7)

1. Create `/src/models/order.js`
  ```js
  import mongoose from "mongoose";

  const orderSchema = mongoose.Schema({
    name: {
      type: String,
      required: [true, "name is required"],
    },
    order: {
      type: [String],
      required: [true, "order is required"],
      default: [],
    },
    isReady: {
      type: Boolean,
      required: true,
      default: false,
    },
    receiptId: String,
  });

  export default mongoose.model("order", orderSchema);
  ```

---
transition: slide-left
---

# Exercise: Make rest of CRUD functionality

1. Test our app so far: do a GET request to /api/order endpoint - do you get JSON back?
1. Implement the rest of the TODOs I listed in the comments (Cmd + Shift + F > search for 'todo').  (i.e. router, controller, handler ...) 


---
transition: slide-left
---

# Recap & Review: Routes (pg.9)

---
layout: image-right
transition: slide-left
image: /assets/moss.png
backgroundSize: 500px 110px
class: text-left
---

# 10 minute break

🍦 Cool Tips, Trends and Resources:
- 🏃 [Mongoose Getting Started](https://mongoosejs.com/docs/)
- 🗺️ [Mongoose Guides](https://mongoosejs.com/docs/guides.html)
- ✅ [Mongoose Validation](https://mongoosejs.com/docs/validation.html)

<br>
<hr>
<br>

- 🧪 [Enter anonymous lab questions](https://docs.google.com/forms/d/e/1FAIpQLSevvGARdHQikso-uLqFCO481MABKE5HofuSrlzEPMNQ2ZLykw/viewform?usp=dialog)
- ℹ️ [Course feedback survey](https://circuitstream.typeform.com/to/ZoyYk7px#course_id=SoftwareAN&instructor=9514)

<!-- 
- take attendance
-->

---
transition: slide-left
---

# Exercise #3: CRUD with Models
(30 min) 

Time to put our previous foodtruck Express app, with mongoose/mongo and put it all together.  Your mission: Implement the following CRUD operations for our Express [food truck/orders app](https://github.com/avcoder/food-truck-express).  May ask ChatGPT: "in learning mongoose/mongodb and express, how do i incorporate a basic find command inside a route?"

- Read ["Querying" section](https://mongoosejs.com/docs/models.html#querying)
   - Test it out; convince yourself it works in your basic mongoose index.js file
   - Create an express foodtruck endpoint that returns all data 
- Read ["Deleting" section](https://mongoosejs.com/docs/models.html#deleting)
   - Test it out... then Create an express foodtruck endpoint that deletes based on id (or name)
- Read ["Updating" section](https://mongoosejs.com/docs/models.html#updating)
   - Test it out... then Create an express foodtruck update endpoint based on search/replace criteria

💡 Apply CRUD operations to your personal project 


<!--
-->


---
transition: slide-left
---

# Exercise #4: 
(remainder of time) Incorporate a basic front end page to test your work so far

Download or `git clone` my [basic HTML table page](https://github.com/avcoder/basic-table) that should work for 💡 your project idea 💡 (and/or food truck app)

- Put it in a new folder which you can launch with "Go Live"
- Modify the table columns to fit your project app's needs
- Add client-side JavaScript (can use ChatGPT to help scaffold some quick ideas) to fetch from your express endpoints
- Challenge #1: display all data upon requesting from your GET all endpoint

Stretch goals below:
- Challenge #2: delete a row upon requesting from your DELETE endpoint **
- Challenge #3: update a row upon requesting from your UPDATE endpoint
- Challenge #4: create a new row upon requesting from your POST endpoint
- Challenge #5: filter data when a user types a keyword in the search box

** Discuss: How should you handle the response?  Fetch GET / again to get all rows? or frontend solution? 



<!--
-->

---
transition: slide-left
---

# Homework

- Make a To-Do List App with MongoDB [see instructions in LMS](https://courses.circuitstream.com/d2l/le/lessons/9514/topics/49825)