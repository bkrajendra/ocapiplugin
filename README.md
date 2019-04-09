## iPrice RESTful API Generator for October CMS
Generate secure RESTful APIs for your models.

---
### Features

#### JWT-Based Authentication
* Choose to secure your resource endpoints with JWT-based authentication or not.

#### Resource Relationship Eager-loading 
* Set which relationships should always be eager-loaded on `GET` requests. Visit the [October CMS Database Relationships](https://octobercms.com/docs/database/relations) on how to set-up relationships.

#### GET
* Get a collection of resources.
* Get a single resource.
* Filter by column using query parameters or route parameters.
* Paginate using the `page` query parameter with configurable `limit` query parameter.

#### POST
* Create a resource.

#### PUT
* Update a resource.

#### DELETE
* Delete a resource.

---
### Usage

#### Creating/editing a resource endpoint
The resource form has the following fields:
* **Base Endpoint (required):** the base url of the endpoint you want to create. Examples are `api/v1/blogs`, `api/v1/{category}/blogs`. You can have a route parameter as in the example (`{category}`) but make sure the parameter name is an actual field in your resource table and resource name (`blogs`) is placed at the end.
* **Model Class (required):** the Eloquent model class you want to expose in the API as a resource.
* **Eager Load Relationships:** the relationships specified in your Eloquent model class you want to eager-load on every `GET` request.
* **Require Authentication:** check this if you want to protect your resource API with  JWT-based authentication.

#### Using your resource API
##### `GET` requests
To request a collection, do something like the following:
```
# Basic collection
GET example.com/api/v1/blogs

# Filtered collection.
# note: you can use any fields from the resource as filter
# and you can use as many filters as you want.
GET example.com/api/v1/blogs/?category=tech
```
By default, the API will return the first 10 results. If you want to get the next batch or return more than the default limit, you can use the `page` and/or `limit` query parameters like the following:
```
# Get the first 20 results.
GET example.com/api/v1/blogs/?limit=20

# Get the next 20 results.
GET example.com/api/v1/blogs/?page=2&limit=20

# You can use the `page` and `limit` query parameters
# along with any filters you need.
# Filter by category and get the results from page 2.
GET example.com/api/v1/blogs/?category=tech&page=2
```
If you specified a route parameter in your endpoint, eg: `api/v1/{category}/blogs`, you need to specify a value in place of the route parameter:
```
GET example.com/api/v1/tech/blogs
```
You can still use pagination and filters along with route parameters.

To get a single resource, specify the resource ID after the base endpoint:
```
GET example.com/api/v1/blogs/1
GET example.com/api/v1/tech/blogs/1
```

#### `PUT` requests
You can update a resource by doing a `PUT` request to a specific resource with a payload:
```
PUT example.com/api/v1/blogs/1 -H 'content-type:appilcation/json' -d '{"title":"Updated blog title"}'
```

#### `DELETE` requests
You can delete a resource by doing a `DELETE` request to a specific resource:
```
DELETE example.com/api/v1/blogs/1
```

#### Authentication
If you chose to protect your API endpoints with authentication, you need to acquire a token by logging in and use that token on every API request you make.

_To manage API users, please refer to [RainLab User](https://octobercms.com/plugin/rainlab-user) plugin._

To log in, make a `POST` request to the `api/auth/login` endpoint with a `login` and `password` payload:
```
# Acquire a token (login)
POST example.com/api/auth/login -H 'content-type:application/json' -d '{"login":"user@example.com","password":"password123"}'
```
If successful, it will return something like the following:
```
{
    "token": "some.jw.token",
    ...
}
```
Use the value of the `token` field as a bearer token in the header or as a `token` query parameter:
```
# As a header
GET example.com/api/v1/blogs -H 'authorization: bearer some.jw.token'

# As a query parameter
GET example.com/api/v1/blogs/?token=some.jw.token
```