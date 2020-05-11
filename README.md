# Poly Lens API Docs

## Getting Started

This quick intro will help you get started using the Poly Lens GraphQL APIs.

## API Keys

First, you'll need to aquire an API key to be able to access the Poly Lens APIs.  Your 
Poly Lens contact person should be able to help provide both an `API_KEY` and `API_SECRET`. 
Once you have them continue on to the next steps.

## Access Tokens

In order to make API calls to the Poly Lens APIs you'll to authenticate and aquire an `access_token` which comes in the form of a JWT.  In order to obtain an `access_token` you need to authenticate to our authentication API.  How you do this depends on which type of application you intend to build; there are two types: user-based and machine-based.

### User Centric Applications

If you intend to build an application that will act on behalf of a Poly Lens user then you'll need to forward the user into our OAuth workflow. The user will be redirected back to you with an access token if they successfully login.

You can follow the instructions here (https://auth0.com/docs/api/authentication#introduction) to get going.

### Machine-to-Machine Applications

If you intend to build an application that acts on its own behalf (machine to machine) then you can login using a direct HTTP request like below

```
curl --request POST \
  --url 'https://https://silica-stage01.auth0.com/oauth/token' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data grant_type=client_credentials \
  --data 'client_id=YOUR_CLIENT_ID' \
  --data client_secret=YOUR_CLIENT_SECRET \
  --data audience=https://api.silica-stage01.io.lens.poly.com/

```

The response will look something like

```
{
  "access_token": "<JWT_TOKEN>",
  "token_type": "Bearer"
}
```

## Querying the GraphQL APIs

Now that you have an access token you'll be able to make requests to the Poly GraphQL API endpoint.  In order to do so you'll need to make a post request to https://api.silica-stage01.io.lens.poly.com/graphql
with an `Authorization: Bearer <ACCESS_TOKEN>` header.

For example, to query for the details of your account you'd use the `tenants` query API:

```
curl --request POST \
  --url 'https://api.silica-stage01.io.lens.poly.com/graphql' \
  -H "Content-Type: application/json" \
  --data '{ "query": "{ tenants { id name } }" }' 
```

This query is an important first step because knowing the id of your account (tenant) is important for making other kinds of queryies and mutations.

From here on we'll only specify the `query` part of the POST for brevity.

### Getting a list of your Accounts

Query
```
{ 
  tenants { 
    id name 
  } 
}
```

Response
```
{
  "data": {
    "tenants": [
      {
        "id": "...",
        "name": "Account 1"
      },
      {
        "id": "...",
        "name": "Account 2"
      },
      ...
    ]
  }
}
```

### Getting a List of Sites for your Account

Query
```
{ sites { id name  tenant { id }} }
```

Response
```
{
  "data": {
    "sites": [
      {
        "id": "...",
        "name": "Site 1",
        "tenant": {
          "id": "..."
        }
      },
      {
        "id": "...",
        "name": "Site 2",
        "tenant": {
          "id": "..."
        }
      },
      ...
    ]
  }
}
```

### Getting a list of devices

Request
```
{ devices { id name room { id site { id } } } }
```

Response
```
{
  "data": {
    "devices": [
      {
        "id": "...",
        "name": "Device 1",
        "room": {
          "id": "...",
          "site": {
            "id": "..."
          }
        }
      },
      {
        "id": "...",
        "name": "Device 2",
        "room": {
          "id": "...",
          "site": {
            "id": "..."
          }
        }
      }
    ]
  }
}
```

### Adding a device to a room

Request

```
mutation {
  updateDevice(
    id: "00e0db5005f8", 
    fields: {
      roomUUID:"c2f1ca52-8748-48d4-84ba-1d3a2bbe2f19"}
  ) {
    id name room {
      id
    }
  }
}
```


Response
```
{
  "data": {
    "updateDevice": {
      "id": "00e0db5005f8",
      "name": "Mo Problems",
      "room": {
        "id": "c2f1ca52-8748-48d4-84ba-1d3a2bbe2f19"
      }
    }
  }
}
```