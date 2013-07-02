---
layout: post
title: Associations in ID Style JSON API
---

Associations and relationships can be represented in several ways in a JSON API.
Simple one-to-one and one-to-many embedded associations were introduced in <a href="{% post_url 2013-06-12-id_style_json_api %}">ID Style JSON API</a>.

    /api/users/1

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

An alternative approach is to include an email_address_id attribute user document.
A separate API request would be required to retrieve the associated email_address.

    /api/users/1

    {
      "user": {
        "id": 1,
        "name": "Foo Baz",
        "email_address_id" : 2
      }
    }

    /api/email_addresses/2

    {
      "email_address" : {
        "id": 2,
          "email": "foo@example.com"
        }
      }
    }

A similar approach can be used for one-to-many relationships, with an email_address_ids attribute -

    /api/users/1

    {
      "user": {
        "id": 1,
        "name": "Foo Baz",
        "email_address_ids" : [2, 3]
      }
    }

The inefficient way to retrieve the email_addresses would be individually by id, with a separate API request for each -

    /api/email_addresses/2

    {
      "email_address" : {
        "id": 2,
          "email": "foo@example.com"
        }
      }
    }

    /api/email_addresses/3

    {
      "email_address" : {
        "id": 3,
          "email": "foo@work.com"
        }
      }
    }

The API could mitigate the performance problems here by including multiple documents in the API response -

    /api/email_addresses?ids=2,3

    {
      "email_addresses": [
        {
          "id": 2,
          "email": "foo@example.com"
        },
        {
          "id": 3,
          "email": "foo@work.com"
        },
      ]
    }


Client and server would need to agree on the URL scheme to map between email_address_id 1 and /api/email_addresses/1.
This would need to be hardcoded in the client. In simple cases like this the convention is relatively straightforward
(email_address_id maps to an email_address, email_address_ids map to individual email_addresses).

An alternative to multiple API requests is <strong>side-loading</strong> (or compound documents).
In this approach the associated documents would be returned along with the requested document.
The overall JSON document would have multiple root elements -

    /api/users/1

    {
      "user": {
        "id": 1,
        "name": "Foo Baz",
        "email_address_ids" : [2, 3]
      },
      "email_addresses": [
        {
          "id": 2,
          "email": "foo@example.com"
        },
        {
          "id": 3,
          "email": "foo@work.com"
        },
      ]
    }

Here the client would be able to map email_address_ids to the associated email_addresses within the same document.

The [active_model_serializers](https://github.com/rails-api/active_model_serializers) gem implements both the embedded and side-loading approaches described here.
A subsequent post will describe an alternative approach proposed for ember-data at [jsonapi.org](http://jsonapi.org/format/#id-based-json-api).

References:

* [active_model_serializers](https://github.com/rails-api/active_model_serializers)
* [jsonapi.org](http://jsonapi.org/format/#id-based-json-api)