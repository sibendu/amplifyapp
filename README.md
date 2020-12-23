## 1. Create a User Identity Pool - oss

Domain Name: set a domain name after as per availability (oss; restof the steps use this value)
https://oss.auth.eu-west-1.amazoncognito.com

## 2. Resource Server: 

Define scopes.  e.g. oss/test

## 3. App clients: two app clients registers

a) web_client :  id = 75h5m8k..... -> to be used by web/ mobiles; 
## DO NOT use secret. 
	In App Client Setting:   allowed 'Authorizaton_code', 'Implicit_Grant'	and oAuth scope (user, email,..)
	Used dummy url for callback and sign out (e.g. https://www.google.com ; same to be used below)

b) system_client : id = 2a3d185l....  secret = 1m4hqn1....   -> this is to be used for application clients and uses secret.
	In App Client Setting:   allow 'client_credentials' and associate with scope defined in resource server (oss/test)

## 4. Now we can register new users, and login: 

https://oss.auth.eu-west-1.amazoncognito.com/login?response_type=code&client_id=<web_client_id e.g. 75h5m8k.....>&redirect_uri=https://www.google.com


## 5. Now we can authorize, get token, get user info using API 

(https://docs.aws.amazon.com/cognito/latest/developerguide/token-endpoint.html) 

### a) Authorize:
------------
GET https://oss.auth.eu-west-1.amazoncognito.com/oauth2/authorize?response_type=code&client_id=<web_client_id e.g. 75h5m8k.....>&redirect_uri=https://www.google.com

Redirects to: https://www.google.com/?code=ec8f35bd-e3fa-4d7c-a3a4-01f2fcba8547  (It is the Authorization Code)

### b) Token:
------------

#### For using the 'system_client' : 
POST https://oss.auth.eu-west-1.amazoncognito.com/oauth2/token
Header: Authorization=Basic <client_id:client_secret above> and Content-Type='application/x-www-form-urlencoded'
Body: grant_type=client_credentials&scope=oss/test

Returns following response:
{
    "access_token": "ey.......O4e7rA",
    "expires_in": 3600,
    "token_type": "Bearer"
}

Note: when making call using Postman, take care URL has no additional space, it was giving error dueto that !

#### For using the 'web_client' : 
POST https://oss.auth.eu-west-1.amazoncognito.com/oauth2/token
Header: Content-Type='application/x-www-form-urlencoded'
Body: grant_type=authorization_code&client_id=<web_client_id>&redirect_uri=https://www.google.com&code=<AuthCode-received-step-above>

Returns following response:
{
    "id_token": "eyJ...BVSg",
    "access_token": "eyJra...PiZxfNQ",
    "refresh_token": "eyJjdHk...IJeoQ",
    "expires_in": 3600,
    "token_type": "Bearer"
}

### Get User Info:
------------
GET https://oss.auth.eu-west-1.amazoncognito.com/oauth2/userInfo
Header: Bearer Token <access-token from step above>

Returns:
{
    "sub": "79740408-f510-4804-....",
    "email_verified": "true",
    "phone_number_verified": "false",
    "phone_number": "+91*********",
    "email": "***@gmail.com",
    "username": "meh**"
}

## 6. For making use of this React UI app

Edit: src/aws-exports.js - set the details of Cognito use pool

Run:
npm install 
npm start

Now access: http://localhost:3000
