# Background

## Task:
Create a web application that allows users to create, read, update and delete (CRUD) data from a Purchase Order database.

Clients are able to 
- List parts, 
- Prepare a purchase order, 
- Submit a purchase order for a number of parts
- Query the status of a purchase order

Functionality:
- List parts for sale
  - Returns list of parts, including part number, description, etc.
  - Excludes quantity on hand
- Find information about a specific part given the part number.
- List information about purchase orders
- Prepare a purchase order
    - User enters information about the purchase order, including part number, quantity, etc.
- Submit a purchase order by invocation of a method that takes the purchase order number as a parameter.


## API Endpoints

### GET

##### All parts:

`/parts`

ex:

[http://localhost:3000/parts](http://localhost:3000/parts)

[https://postgres-express-rest.liamo2.repl.co/parts](https://postgres-express-rest.liamo2.repl.co/parts)


##### Part by part number:

`/parts/number/:number`

ex:

[http://localhost:4000/parts/number/1](http://localhost:4000/parts/number/1)

[https://postgres-express-rest.liamo2.repl.co/parts/number/1](https://postgres-express-rest.liamo2.repl.co/parts/number/1)

#### Purchase Orders:

##### All purchase orders:

`/pos`

ex:

[http://localhost:4000/pos](http://localhost:4000/pos)

[https://postgres-express-rest.liamo2.repl.co/pos](https://postgres-express-rest.liamo2.repl.co/pos)

##### Purchase order by purchase order number (po_number):

`/pos/number/:number`

ex:

[http://localhost:4000/pos/number/1](http://localhost:4000/pos/number/1)

[https://postgres-express-rest.liamo2.repl.co/pos/number/1](https://postgres-express-rest.liamo2.repl.co/pos/number/1)

#### POST

##### Create a new part:

`/parts`

ex:

[http://localhost:4000/parts](http://localhost:4000/parts)

[https://postgres-express-rest.liamo2.repl.co/parts](https://postgres-express-rest.liamo2.repl.co/parts)

Post body:
|Key|Value|
|---|---|
|`part_name`   | text |
|`part_description` | text  |
|`quantity_on_hand` | int  |

`part_number` is automatically generated.

##### Create a new purchase order:



### Technical Inventory
* **Node.js**: A JavaScript runtime engine.
* **Express**: A web application framework for Node.js.
  * **EJS**: A templating engine for Node.js.
* **PostgreSQL**: An open-source relational database management system.
  * **Supabase**: A service that provides free database hosting for learning and prototyping.


### Node.js:
Node.js is an open-source, cross-platform, JavaScript runtime environment that executes JavaScript code outside of a browser. Node.js lets developers use JavaScript to write command line tools and for server-side scripting. Node.js is part of the "JavaScript everywhere" paradigm, unifying web application development around a single programming language, rather than different languages for server-side and client-side scripts. [Here is a guide to getting started with Node.js](https://nodejs.org/en/docs/guides/getting-started-guide/).


### Express.js
Express has been called the de facto standard server framework for Node.js. Express provides a minimal and flexible set of features for creating web applications and APIs. It provides a set of features like routing, middleware, view system, etc. It simplifies the server creation process that can be used to create a web application more easily and quickly. [Here is a guide to getting started with Express.js](https://expressjs.com/en/starter/installing.html).


### Creating a .env file:

To store environment variables, create a .env file in the root directory of your project. The .env file should contain the following variables:

```
DB_PASS=[your database password]
DB_URL=[your database url]
```

If you are working with a local copy of the database, DB_URL will be something like localhost:5432. If you are working with a remote database, DB_URL will be something like [your-project-name].supabase.co.

### PostgreSQL

Install the `pg-promise` package:

```bash
npm install pg-promise
```

Include the `pg-promise` package in the list of required modules in `app.js`:

```js
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');
var pgp = require('pg-promise')();
```

Then, require set the values database password and url from the environment variables in `app.js` file:
```js
var dbPass = process.env.DB_PASS;
var dbUrl = process.env.DB_URL;
```

Then create a new database object named `db` using the `pgp` function and the database url:
```js
var db = pgp(`postgres://postgres:${dbPass}@${dbUrl}:5432/postgres`)
```

## Querying the Database:

A simple example of a query is retrieving all of the data from the `parts925` table. This can be done by specifying the route and the query in `routes/parts.js`:
```js
var express = require('express');
var router = express.Router();

var db = require('../database/db');

/* GET all parts */
router.get('/', function(req, res, next) {
    db.any(`SELECT part_number, part_name, part_description FROM public.parts925`, [true])
        .then(function(data) {
            res.json(data);
        })
        .catch(function(error) {
            res.send(error);
        });
});

module.exports = router;
```

This file is referenced in `app.js`:
```js
var partsRouter = require('./routes/parts');

app.use('/parts', partsRouter);
```

This loads the data from the `parts925` table and displays it in JSON format at the `/parts` route.

So, if you are to visit the base URL of the project (by default runs on port 4000), you will see the data from the `parts925` table displayed in JSON format:

[http://localhost:4000/parts](http://localhost:4000/parts)

[https://postgres-express-rest.liamo2.repl.co/parts](https://postgres-express-rest.liamo2.repl.co/parts)

Say for instance you wanted to retrieve the data from the `parts925` table where the `id` is equal to 1. You can do this by specifying the route and the query in `routes/parts.js`:
```js
/* GET parts by part number */
router.get('/number/:number', function(req, res, next) {
    var partNumber = req.params.number;

    db.any(`SELECT part_name, part_number, part_number_cpu, part_description FROM public.parts925 WHERE "part_number" = $1 `, [partNumber])
        .then(function(data) {
            res.json(data);
        })
        .catch(function(error) {
            res.status(500);
            res.send(error);
        });
});
```

`/number/:number` corresponds to the route `parts/number/` followed by a part number. So, if you are to visit the following URL, you will see the data from the `Parts925` table where the `part_number` is equal to the part number specified in the URL:

[http://localhost:4000/parts/number/1](http://localhost:4000/parts/number/1)

[https://postgres-express-rest.liamo2.repl.co/parts/number/1](https://postgres-express-rest.liamo2.repl.co/parts/number/1)

This will provide a JSON response with the data from the `Parts925` table where the `part_number` is equal to 1:

```json
```

## Creating a new part:

To create a new part, you will need to create a new route in `routes/parts.js`. This route will be a POST request to the `/` route. This route will accept the following parameters:

part_name: string
part_description: string
quantity_on_hand: integer

```js
router.post('/', function(req, res, next) {
    var partName = req.body.part_name;
    var partDescription = req.body.part_description;
    var quantityOnHand = req.body.quantity_on_hand;

    console.log(partName, partDescription, quantityOnHand);

    db.any(`
        INSERT INTO public.parts925
            (created_at, part_name, part_description, quantity_on_hand)
        VALUES(now(), $1, $2, $3);
        `, [partName, partDescription, quantityOnHand])
        .then(function(data) {
            console.log(data);
            res.json(data);
        })
        .catch(function(error) {
            console.log(error);
            res.send(error);
        });
});
```

Updating a part:

To update a part, you will need to create a new route in `routes/parts.js`. This route will be a PUT request to the `/parts/number` route. This route will accept the following parameters:

part_name: string
part_description: string
quantity_on_hand: integer

Example: submit a PUT request to `/parts/number/1` with the following parameters:
|  Key | Value  |
|---|---|
|  part_name | text |
|  part_description | text |
|  quantity_on_hand | int |


```js
router.put('/number/:number', function(req, res, next) {
    var partNumber = req.params.number;
    var partName = req.body.part_name;
    var partDescription = req.body.part_description;
    var quantityOnHand = req.body.quantity_on_hand;

    db.any(`
        UPDATE public.parts925
        SET part_name=$2, part_description=$3, quantity_on_hand=$4
        WHERE part_number=$1;
    `, [partNumber, partName, partDescription, quantityOnHand])
    .then(function(data) {
        res.json(data);
    })
    .catch(function(error) {
        res.status(500);
        res.send(error);
    });
});
```

Retrieving a purchase order:

```js
/* GET parts by part number */
router.get('/number/:number', function(req, res, next) {
    var partNumber = req.params.number;

    db.any(`
        SELECT * 
        FROM public.pos925 
        WHERE "po_number" = $1
        `, [partNumber])
        .then(function(data) {
            res.json(data);
        })
        .catch(function(error) {
            res.status(500);
            res.send(error);
        });
});
```

Deleting a part:

```js
router.delete('/number/:number', function(req, res, next) {
    var partNumber = req.params.number;

    db.any(`
        DELETE FROM public.parts925
        WHERE part_number=$1;
    `, [partNumber])
    .then(function(data) {
        res.json(data);
    })
    .catch(function(error) {
        res.status(500);
        res.json(error);
    }); 
});
```

Deleting a purchase order:

```js
router.delete('/number/:number', function(req, res, next) {
    var poNumber = req.params.number;
    
    db.any(`
        DELETE FROM public.pos925
        WHERE po_number=$1;
        `, [poNumber])
        .then(function(data) {
            res.json(data);
        })
        .catch(function(error) {
            res.status(500);
            res.json(error);
        }
    );
});
```



### Deployment:

#### Github

For ease of integration with Repl.it, I have deployed the application on Github. You can view the publi c repository [here](https://github.com/LiamOsler/postgres-express-REST)

#### Repl.it

For ease of access, I have deployed the application on the online IDE/deployment platform Repl.it. You can view the application [here](https://postgres-express-rest.liamo2.repl.co/) and you can view the IDE with the code [here](https://replit.com/@LiamO2/postgres-express-REST). Note, if the application is not dormant, it may take a second to start running.

# References

## Blogs, tutorials and videos:

Net Ninja Express Tutorials:
https://www.youtube.com/watch?v=yXEesONd_54

## Documentation:

Node.js:
https://nodejs.org/en/

Express:
https://expressjs.com/en/guide/using-template-engines.html

