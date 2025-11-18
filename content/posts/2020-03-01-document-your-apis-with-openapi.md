+++
title = 'Document Your APIs With OpenAPI'
categories = ['apis']
[hero]
  id = 'jb1Mc1lv8X0'
  url = 'https://unsplash.com/photos/jb1Mc1lv8X0'
  credit = 'Karsten Würth'
+++
Over the past 5 years I've worked on an API first application full time. And
over those 5 years we've learned a lot about API design. In this post I'm going
to focus on documentation, how we approach it, and some of the tools we use.

## Document First

Previous projects at our company used [documentation generation tools](https://github.com/nelmio/NelmioApiDocBundle).
These tools are useful because they tie into your code and don't take a lot of
work to generate documentation. The documentation also updates as your make
changes in code. While there are positives to these tools, there are some drawbacks.

If you use a documentation generation tool, documentation comes after you write
code. A good API is designed before you write any code. Take time to think
through all the use cases. Sit down with the people that are going to be using
your API. Start documenting and iterating on that design.

After you've come to an agreement on the documentation, you have an artifact that
all parties can start building to. The backend team can start building out the
database models at the same time frontend or mobile developers build out their
integration. You can even use that documentation to generate [mock data](https://openapi.tools/#mock)
to work with.

Another benefit is that your API doesn't become tightly coupled to your database.
While this isn't necessarily bad, it can cause issues later down the road if
your database schema changes.

## Stick With The Standards

API documentation has come a long ways in five years. When we started working on
our API, the options were Swagger, RAML, Blueprint, SASS solutions, or to build
our own. Swagger had some trouble spots for us (or probably a lack of
understanding the specification) and the other systems were too complex. Against
our better judgment, we wrote our spec using a static site generator. It gave us
a nice documentation site, but when it came to tooling, we were on our own.

Had we stuck with Swagger from the beginning and not given up at the first issue
we encountered, we would be in a much better spot today. We still have endpoints
using our old documentation and making updates to those takes extra effort
because of it.

We learned our lesson and started writing our new API specification in OpenAPI.
Working with our spec has become much easier. By using OpenAPI, we can use
third party tools and libraries that support OpenAPI. You can automatically
generate SDKs, run validation, mocking data, and [more](https://openapi.tools/).

## Our Process

Before I talk about our process, we should talk about how we organize our
specification. We are potentially working on multiple features at once. To prevent
issues with merge conflicts, we split up our specification files and stitch them
together. To give you an idea, here is the basic setup of our _spec_ directory.

    spec/
        _base.yaml # Contains the base structure, info, and common schemas
        animals.yaml # Contains endpoints and schemas related to animals
        internal/ # Contains files for internal use endpoints
            users.yaml # Contains endpoints and schemas related to users

Here is our __base.yaml_ file. This contains the structure and basic components
that most endpoints use.

```yaml
# _base.yaml
openapi: "3.0.2"
info:
  title: Awesome API
  description: Description for the awesome API
  version: "1.0.0"
servers:
  - url: https://api.example.com/v2
  - url: http://api.localhost/api/v2
    description: local
    x-internal: true
tags:
  - name: Animal
    description: Endpoints for animals
  - name: User
    description: Endpoints for user management
    x-internal: true
paths: {} # other files will get merged here
components:
  responses:
    Error:
      description: Error
      # rest of response
  schemas: # Common schemas
    Pagination:
      type: object
      # rest of common schemas
```

Then we create a file for each collection of endpoints. These files contain
`path` and `components`.

```yaml
# animals.yaml
paths:
  /animals:
    get:
      operationId: animals.list
      # rest of the spec
components:
  schemas:
    Animal:
      type: object
      properties:
        # rest of the schema
```

To get a single file, we use a [npm module](https://github.com/ivorisoutdoors/openapi-stitcher)
that I wrote. It deep merges files matching a pattern and outputs to a single
file.

    npx openapi-stitcher build "spec/**/*.yaml" openapi.yaml

### Planning

When we get requirements for a project we'll start writing the spec. During this
we've found a [UI](https://github.com/swagger-api/swagger-ui) to be useful, so
we've built one into our stitcher. Passing the `--watch` flag will reload the
UI when it detects changes.

    npx openapi-stitcher serve --watch "spec/**/*.yaml"

We store our specification in our API codebase. This allows us to version our
documentation with our API. It also has the benefit of being able to use merge
requests to discuss and approve the proposed (or changed) specification.

### Linting

The OpenAPI specification can be a little hard to start writing. How can you make
sure what you're writing is valid? We use a linter! We were using Speccy, but
switched to [Spectral](https://stoplight.io/open-source/spectral/). It validates
the specification and does some checks for best practices.

We have a step in our continuous integration pipeline that validates our
specification is valid, making sure we're always shipping a quality specification.

### Publishing

When looking to publish your OpenAPI specification, there are tons of
[options](https://openapi.tools/#documentation). One of the last steps on our
continuous integration pipeline is publishing a UI to an S3 bucket. Now anyone
can view the documentation without having to run the UI locally.

We have both public and internal endpoints. Our stitcher tool use to take care
of excluding files when creating the spec. But we've found a better way to
do this with [another npm module](https://github.com/Mermade/openapi-filter).
OpenAPI allows for custom tags starting with `x-`. Anything that has an
`x-internal` tag is removed from the final specification.

    npx openapi-filter openapi.yaml openapi.public.yaml

### Testing

Documentation is hard. External documentation is even harder. It's easy to
forget to update as your code changes. To prevent this, we integrate our OpenAPI
specification to our functional tests. When we test our endpoints, we use
[validators](https://openapi.tools/#data-validators) to make sure what's
returned, matches what we've documented.

## Where To Start

If you're new to OpenAPI, first check out the [official site](https://www.openapis.org/)
and read up on the [specification](https://spec.openapis.org/oas/v3.0.2).

If you want to start writing a spec with little to no knowledge, checkout
[Stoplight Studio](https://stoplight.io/studio) or other [GUIs editors](https://openapi.tools/#gui-editors).
These assist in the process of writing endpoints and model without needing to
be familiar with OpenAPI.

For a list of other OpenAPI compatible tooling, check out [OpenAPI.Tools](https://openapi.tools/).
