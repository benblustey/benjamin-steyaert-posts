---
title: NodeJS MongoDB API
date: 2024-07-20
categories: webdev, database
tags:
  - docker
  - NodeJS
  - MongoDB
  - homelab
image: /assets/img/posts/nodejs-mongo.jpg
---

Self hosting an REST API is much easier than it sounds and provide a very useful and powerful tool in the homelab toolbox. Below is an example NodeJS API with MongoDB that was built for the Frigate Event data visualization project.

**View the source code at [nodejs-express-mongodb](https://github.com/benblustey/nodejs-express-mongodb)**

```
server.js
|-- app/
|   |-- config/
|   |   |-- auth.config.js
|   |   |-- db.config.js
|   |-- controllers/
|   |   |-- articles.controller.js
|   |   |-- auth.controller.js
|   |   |-- events.controller.js
|   |   |-- user.controller.js
|   |-- middlewares/
|   |   |-- authJwt.js
|   |   |-- index.js
|   |   |-- verifyEvents.js
|   |   |-- verifySignup.js
|   |-- models/
|   |   |-- articles.controller.js
|   |   |-- auth.controller.js
|   |   |-- index.js
|   |   |-- events.controller.js
|   |   |-- user.controller.js
|   |-- routes/
|   |   |-- articles.routes.js
|   |   |-- auth.routes.js
|   |   |-- event.routes.js
|   |   |-- user.routes.js
|-- tests/
|-- | events.test.ts
```

* Currently not utilizing the articles model. This will be used in the future for adding additional content.

The below specific routes are used to CRUD the events collection. The `id` variable is the `epoch` string value since there cannot be multiple events at the same time. This was a simple way to remove the chance of a duplicate video clip being added. The `/api/event/all` route will return the entire JSON of all events. 

#### Events Routes
``` javascript
// GET ALL EVENTS
app.get("/api/event/all", controller.allEvents);
// GET AN EVENT
app.get("/api/event/:id", controller.singleEvent);
// ADD AN EVENT
app.post("/api/event/add",
		 [ verifyEvents.checkDuplicateEvents ],
		 controller.addEvents);
// UPDATE AN EVENT
app.put("/api/event/update/:id", controller.updateEvent);
// DELETE AN EVENT
app.delete("/api/event/delete/:id", controller.deleteOneEvent);
```

#### Events Model
``` json
{
  "src": String,
  "epoch": String,
  "date": String,
  "friendlyDate": String,
  "length": Number,
  "starred": Boolean
}
```

#### Testing
Currently there is not testing implemented. This is needed before publishing or expanding this project into a boilerplate. Items to look into:
* supertest
* JEST

## Whats Next?
- Create a frontend utilizing AstoJS and React for data visulaization
