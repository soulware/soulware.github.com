---
layout: post
title: API Versioning through Content Negotiation
---

Content negotiation supports multiple API versions with a single set of API endpoints.
It is the best way to provide backward compatibility without forcing clients to manage versioned endpoints.

The _simple_ way is to version the URI itself -

    http://example.com/api/v1/accounts/1

Each resource will be available on multiple different URIs as the API version number changes.
Clients will be forced to handle multiple endpoints for a given resource.
URIs stored by the client may need to be rewritten as the version number changes.
Don't do this.

Use the accept header to request a specific version of the API by including the version in the media type -

    Accept: application/vnd.com.example.v1+json

An accept header `application/json`, `text/html` can be used to specify the generic media type.
A vendor media type `application/vnd.com.example+json` can be used to request
a document that conforms to an particular known format.
This can be taken be taken a step further to include the version in the media type `application/vnd.com.example.v1+json`.

With this approach multiple API versions can be accessed with a single URI -

    http://example.com/api/accounts/1

A client can request a particular version by specifying the appropriate media type in the accept header -

    Accept: application/vnd.com.example.v1+json


References:

[Haters gonna HATEOAS](http://timelessrepo.com/haters-gonna-hateoas)

[Versioning REST Web Services](http://barelyenough.org/blog/2008/05/versioning-rest-web-services])
