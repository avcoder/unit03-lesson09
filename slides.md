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
- [ ] Schema types and Validation, Querying
- [ ] REMINDER: Lab tomorrow

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
1. Test our app so far: do a GET request to /api/order endpoint - do you get JSON back?

---
layout: image-right
transition: slide-left
image: /assets/bos.png
backgroundSize: 500px 300px
class: text-left
---

# 10 minute break

🍦 Cool Tips, Trends and Resources:
- ℹ️ [MDN: using mongoose](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Express_Nodejs/mongoose)
- 🐹 [Mongoose Tutorials](https://www.geeksforgeeks.org/mongoose-tutorial/)
- ▶️ [Intro to MongoDB / Mongoose](https://www.youtube.com/watch?v=-PdjUx9JZ2E)

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

# Group Exercises: Make rest of CRUD functionality
(remainder of time)  Take 10 mins to develop just one of the CRUD functionalities.  I'll take it up each 10 min interval.

1. Implement the rest of the TODOs I listed in the comments (Cmd + Shift + F > search for 'todo').  (i.e. router, controller, handler ...) 
   - delete (Exercise #1 - 10 mins)
   - create / post (Exercise #2 - 10 mins)
   - update / put  (Exercise #3 - 10 mins)
   - Stretch goal : implement the search box to search by name or receipt id


<!--
-->

---
transition: slide-left
---

# Homework

- REMINDER: There is a lab tomorrow
- Make a To-Do List App with MongoDB [see instructions in LMS](https://courses.circuitstream.com/d2l/le/lessons/9514/topics/49825)