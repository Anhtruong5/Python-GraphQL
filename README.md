<img src="https://wagtail.io/static/img/wagtail.dbf60545a188.svg" height="100px">&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://graphql.org/img/logo.svg" height="100px">
<hr/>

# wagtail-graphql
> An app to automatically add GraphQL support to a Wagtail website

This [Wagtail](https://wagtail.io/) app adds [GraphQL](https://graphql.org/) types to other Wagtail apps. The objective is for this library to interact with an existing website in a generic way and with minimal effort.
In particular, it makes minimal assumptions about the structure of the website
to allow for a generic API.

## Installing / Getting started

To install as a general app:

```shell
pip install wagtail-graphql
```

Add it together with [graphene_django](https://github.com/graphql-python/graphene-django) to the Django INSTALLED_APPS:

```python
INSTALLED_APPS = [
    ...
    'wagtail_graphql',
    'graphene_django',
    ...
]

```

### Initial Configuration

Add the required [graphene](https://github.com/graphql-python/graphene) schema `GRAPHENE` and a `GRAPHQL_API` dictionary.
Include all the Wagtail apps the library should generate bindings to in the `APPS` list and optionally specify the prefix for each app in `PREFIX`. To remove a leading part of all the urls for a specific site, specify the `URL_PREFIX` parameter for each needed host.

```python
GRAPHENE = {
    'SCHEMA': 'wagtail_graphql.schema.schema',
}

GRAPHQL_API = {
    'APPS': [
        'home'
    ],
    'PREFIX': {
        'home': ''        # optional, prefix for all the app classes generated by the wrapper
    },
    'URL_PREFIX': {
        'localhost': '/home'   # optional, read from the site information if not specified 
    }
}
```
The example above generates bindings for the `home` app, .  Every url in this example
will be stripped of the initial `/home` substring.

Finally, set up the GraphQL views in the project `urls.py`.
For example, to add two endpoints for GraphQL and the [GraphiQL](https://github.com/graphql/graphiql) IDE: 

```python
from django.views.decorators.csrf import csrf_exempt
from graphene_django.views import GraphQLView

urlpatterns = [
    ...
    url(r'^api/graphql', csrf_exempt(GraphQLView.as_view())),
    url(r'^api/graphiql', csrf_exempt(GraphQLView.as_view(graphiql=True, pretty=True)),
    ...
]
```
Note that the urls above need to appear before the `wagtail_urls` catchall entry.

#### Images

To be able to generate urls for images the following also needs to be included in the project's `urls.py`:

```python
from wagtail.images.views.serve import ServeView

urlpatterns = [
    ...
    url(r'^images/([^/]*)/(\d*)/([^/]*)/[^/]*$', ServeView.as_view(), name='wagtailimages_serve'),
    ...
]
```


### Multi-site configuration
This library works transparently with a multi-site Wagtail install without any extra configuration required.  To strip a custom leading prefix for each site, specify each host in the `URL_PREFIX`.  For exaple, for two hosts `host1.example.com` and `host2.example.com`:

```
GRAPHQL_API = {
    ...
    'URL_PREFIX': {
        'host1.example.com': '/prefix1',
        'host2.example.com': '/prefix2'
    }
    ...
}
```
Note that the prefix for a site is taken from the root page url if a host is not included in the `URL_PREFIX` dictionary. 


## Developing

To develop this library, download the source code and install a local version in your Wagtail website.


## Features

This project is intended to require minimal configuration and interaction. It currently supports 

* [Page models](https://docs.wagtail.io/en/master/topics/pages.html)
* [Snippets](https://docs.wagtail.io/en/master/topics/snippets.html)
* Images
* Documents
* [StreamFields](https://docs.wagtail.io/en/master/topics/streamfield.html) with [Basic Blocks](https://docs.wagtail.io/en/naster/topics/streamfield.html#basic-block-types) and [StructBlocks](https://docs.wagtail.io/en/master/topics/streamfield.html#structblock) 
 

## Contributing

If you'd like to contribute, please fork the repository and use a feature
branch. Pull requests are welcome.

## Links

- Repository: https://github.com/tr11/wagtail-graphql
- Issue tracker: https://github.com/tr11/wagtail-graphql/issues

## Licensing

The code in this project is licensed under MIT license.

