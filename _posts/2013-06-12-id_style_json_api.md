---
layout: post
title: ID Style JSON API
---

The simplest way of handling ids in a JSON API response is to simply treat the id as any other attribute.

    GET /users/1

    {
      "user": {
        "id": 1,
        "name": "Foo Baz"
      }
    }

Embedded has_one or belongs_to associations can use the same approach for ids -

    {
      "user": {
        "id": 1,
        "name": "Foo Baz",
        "email_address" : {
          "id": 2,
          "email": "foo@example.com"
        }
      }
    }

The same approach also works when returning a collection -

    GET /users

    {
      "users": [
        { "id": 1, "name": "Foo Baz" },
        { "id": 2, "name": "Bar Baz" }
      ]
    }

And embedded has_many associations -

    {
      "user": {
        "id": 1,
        "name": "Foo Baz",
        "email_addresses" : [
          { "id": 2, "email": "foo@example.com" },
          { "id": 3, "email": "foo@work.com" }
        ]
      }
    }

This basic approach to ids keeps things simple on the server but the client requires some prior knowledge of the URI scheme
in order to navigate through various API calls.
For example, what is the URI for email_address 2 in the example above?
The URI may very likely be `/email_addresses/2` or `/users/1/email_addresses/2` but the client needs to know the
appropriate URI scheme ahead of time.

References:
* [jsonapi.org](http://jsonapi.org/format/#id-based-json-api)
* [ActiveModel::Serializers](https://github.com/rails-api/active_model_serializers)
