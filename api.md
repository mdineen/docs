# TitanFile API documentation

## API Authentication

TitanFile's API uses HTTP Basic authentication to enable API access.  Your credentials are protected by TLS while being sent.

#### Structure
Requests are sent as an HTTP request header in the format `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

To construct this header, use a base64 encoder to encode your credentials as `encode("user:password")` and append that value to `Basic `, then set that as the value of the header `Authorization`.

## User Provisioning

The user provisioning API allows you to create, update and disable users **for existing subscriptions**.

#### Authentication

The provisioning API expects service account credentials.

### Create User

#### POST Parameters
- subscription_key
  - char(6), **required**
- email
  - char(75), **required**
- full_name
  - text, optional

#### URL
/federate/create/

#### Response type
application/javascript

#### Sample POST
```
POST /federate/create/ HTTP/1.1
Host: app.titanfile.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
Content-Type: application/x-www-form-urlencoded;charset=UTF-8

subscription_key=aBcdEf&email=user@domain.com&full_name=User+Lastname
```

#### Sample success response
```javascript
{ "user_id": "GhijKL" }
```

#### Sample error response
```javascript
{ [ "Valid email required", "Valid subscription key required" ] }
```

### Update User

#### Query Parameters
- user_key
  - char(6), **required**

#### POST Parameters
- full_name
  - text, **required**

#### URL
/federate/update/

#### Response type
application/javascript

#### Sample POST
```
POST /federate/update/GhijKL HTTP/1.1
Host: app.titanfile.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
Content-Type: application/x-www-form-urlencoded;charset=UTF-8

full_name=User+Lastname+Updated
```

#### Sample success response
```javascript
{ "user_id": "GhijKL" }
```

#### Sample error response
```javascript
{ [ "User not found", "User is not configured for API provisioning" ] }
```

### Disable User

#### Query Parameters
- user_key
  - char(6), **required**

#### URL
/federate/disable/

#### Response type
application/javascript

#### Sample GET
```
GET /federate/disable/GhijKL HTTP/1.1
Host: app.titanfile.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
```

#### Sample success response
```javascript
{ "deleted": "GhijKL" }
```

#### Sample error response
```javascript
{ [ "User not found", "User is not configured for API provisioning" ] }
```

## Send Message in a New Channel

#### Authentication

The send API expects credentials for the user sending the message.

### Send Message

#### POST Parameters

- channelName
  - text, **required**
- messageBody
  - text, optional*
- contacts
  - text, optional
- files
  - HTML files parts, optional*

__Note:__ Send requires either __messageBody__ or __one or more files__

#### URL
/outlook/send/

#### Response type
text/html

#### Sample POST
```
POST /outlook/send/ HTTP/1.1
Host: app.titanfile.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
Content-Type: Content-Type: multipart/form-data; boundary=---------------------------9051914041544843365972754266
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="channelName"

Sample discussion channel for staff.
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="messageBody"

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vestibulum est sem, fermentum ac condimentum ac, interdum vitae velit. Maecenas vulputate augue at ante consequat, a tempus lorem gravida. Vestibulum porta leo nec vehicula porta. Vivamus neque ex, bibendum quis mattis id, lobortis sed ligula. Morbi imperdiet libero ac tellus accumsan, sit amet ornare ante elementum.
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="contacts"

sample-a@titanfile.com,sample-b@titanfile.com
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="file"; filename="sample.pdf"
Content-Type: text/plain

Binary content
-----------------------------9051914041544843365972754266--

```

#### Sample success response
```
200 Success.
```

#### Sample error response
```
403 User storage limit exceeded
```

