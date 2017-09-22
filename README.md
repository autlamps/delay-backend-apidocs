# Delay API Service Docs

Docs for delay api endpoints. Currently very much a work in progress.

## Authenticated Requests

Requests to authenticated endpoints __must__ including the `X-DELAY-AUTH` header:

Name | Format | Optional | Description
-----|------|----------|-------------
Auth Token | X-DELAY-AUTH | n | Authentication token for a device. Generated on sign up or sign in

## User Agent

Please set user agent to the following:

Device Type | User Agent 
------------|------------
iOS | Delay-iOS-<$Version>
Android | Delay-Android-<$Version>
Web | Delay-Web-<$Version>

## Endpoints

### Create New user

Create a new user. All fields can be left blank and an anon user will be created.
If you specify an email, you must also spe~cify a password. If a name is specified an 
email and password are required. 

``POST /users``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
name | string | n | Users Name
email | string | n | Users email address
password | string | n | Users password

Note: if email is specified, password must also be specified

#### Response

``` json
{
    "success": true,~
    "result": {
        "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
        "auth_token": "5f12cd09-0f11-4ed7-867d-3e8884948920",
        "created_on": "1501709004"
    },
    "errors": null,
    "meta": {
        //meta info
    }
}
```

May return error code 1002.

### Get Users
##### *Authentication required*

Returns info for all users auth_token has access to.

``GET /users``

#### Response

``` json
{
    "success": true,
    "result": [
        {       
            "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
            "name": "Bobby Tables",
            "email": "bobby.tables@example.com",
            "created_on": "1501709004"
        },
        {       
            "id": "aadd50f0-4063-4c3d-a051-15cde26160f5",
            "name": "Homer Simpson",
            "email": "h.simpson@example.com",
            "created_on": "1501709004"
        }
    ],
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get User Info
##### *Authentication required*

Returns info for a particular user.

``GET /users/:user_id ``

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
        "name": "Bobby Tables",
        "email": "bobby.tables@example.com",
        "created_on": "1501709004"
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Update User Info
##### *Authentication required*

Updates given user info. Note that when changing the password `new_password`, `new_password_repeated` and `old_password` are required. Also note that a change in email address wil trigger a new confirmation email to be sent out before the change is applied.

``PATCH /users/:user_id ``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
first_name | string | y | Users first name
last_name | string | y | Users last name
email | string | y | Users email address
new_password | string | y | Users password
new_password_repeated | string | y | Users new password repeated
old_password | string | y | Users old password


#### Response

``` json
{
    "success": true,
    "result": {
        "before": {
            "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
            "name": "Bobby Tables",
            "email": "bobby.tables@example.com",
            "created_on": "1501709004"
        },
        "after": {
            "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
            "name": "Bobby Tables",
            "email": "b.tables@example.com",
            "created_on": "1501709004"
        }
 
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

May return error code 1003.

### Delete User
##### *Authentication required*

Deletes a given user. This option will revoke all active ``auth_token`` for that user.

``DELETE /users/:user_id ``

#### Response

``` json
{
    "success": true,
    "result": {
        "deleted_on": "1501736051"
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Authenticate User

Returns an access token for a given email and password.

``POST /tokens ``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
email | string | n | Users email address
password | string | n | Users password

#### Response

``` json
{
    "success": true,
    "result": {
        "user_id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
        "auth_token": "5f12cd09-0f11-4ed7-867d-3e8884948920"
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

May return error code 1001.

### Delete Access Token
##### *Authentication required*

Deletes access token.

``DELETE /tokens/:token_id ``

#### Response

``` json
{
    "success": true,
    "result": {
        "user_id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
        "deleted_on": "1501736051",
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Delete All Access Tokens
##### *Authentication required*

Deletes all access tokens associated with the account of the `auth_token` in `X-DELAY_AUTH`.

``DELETE /tokens``

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "7096ca22-435a-470b-9d21-e3ddecbfbc3c",
        "deleted_on": "1501736051",
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get Abnormally Running Trips
##### *Authentication required*

Gets a a list of all services currently running ahead or behind schedule.

``GET /delays``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "date_created": "1501738185",
        "valid_until": "1501738215",
        "trips": [
            {
                "trip_id": "87064d8a-63e6-472e-a6d3-4d9cdd4bc010",
                "route_id": "3077a24a-a01e-4a34-9112-d457bf8701aa",
                "route_long_name": "City Centre to Rosebank Rd via New North Rd",
                "route_short_name": "221",
                "next_stop": {
                    "id": "a221dc69-300d-431d-93ae-ba1bdd7b6539",
                    "name": "100 New North Rd",
                    "lat": -36.86683,
                    "lon": 174.75697,
                    "delay": 412,
                    "scheduled_arrival": "19:24:07",
                    "eta": "19:30:59"
                },
                "vehicle_id": "3096",
                "lat": -36.86598353,
                "lon": 174.75866391
            },
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get All Routes
##### *Authentication required*

Gets all routes currently in the system. In the context of Delay it is almost always unnecessary to use this endpoint. Instead use search and get individual route.

``GET /routes``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "routes": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": ""
            }
        ]
    },
    "errors": {
        "code": 1004,
        "desc": "It is almost always unnecessary to call this endpoint."
    },
    "meta": {
        // meta info
    }
}
```

### Get Individual Route
##### *Authentication required*

Get route by id. 

``GET /routes/:route_id``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "routes": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": ""
            }
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get All Trips for Route
##### *Authentication required*

Get all trips for a particular route.

``GET /routes/:route_id/trips``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "trips": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": "",
                "trip_id": "",
                "headsign": "",
                "trip_begin": "",
                "trip_end": "",
                "days": [ "Mon", "Tue", "Wed", "Thu", "Fri" ],
                "stops": [
                    {
                        "id": "",
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 1,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    },
                                        {
                        "id": "",
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 2,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    }
                ]
            }
        ]
    },
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Search For Routes
##### *Authentication required*

Search for routes similar to given parameters. `params` will be matched against GTFS route_id, route_short_name or route_long_name. Routes similar to multiple parameters in `params` will only be returned once. A `route_type` can also be specified to limit returned routes to those of a given GTFS `routes.route_type`.

Note: search will err on the generous side returning *similar* not *exact* matches to `params`.

``POST /routes/search``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
params | string[] | n | Array of search parameters
route_type | integer | y | GTFS route type. [Learn more here](https://developers.google.com/transit/gtfs/reference/routes-file).

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "routes": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": ""
            }
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get All Trips
##### *Authentication required*

Gets all trips  currently in the system. In the context of Delay it is almost always unnecessary to use this endpoint. Instead use search and get individual route.

Note: Day in this context is the 3 letter day representation i.e Mon,Tue, Thu etc

``GET /trips``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "trips": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": "",
                "trip_id": "",
                "headsign": "",
                "trip_begin": "",
                "trip_end": "",
                "days": [ "Mon", "Tue", "Wed", "Thu", "Fri" ],
                "stops": [
                    {
                        "id": "", 
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 1,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    },
                                        {
                        "id": "",
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 2,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    }
                ]
            }
        ]
    },
    "errors": {
        "code": 1004,
        "desc": "It is almost always unnecessary to call this endpoint."
    },
    "meta": {
        // meta info
    }
}
```

### Get Individual Trip
##### *Authentication required*

Gets trip by id.

``GET /trips/:trip_id``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 1,
        "valid_until": "1502928000",
        "trips": [
            {
                "id": "66407722-89f0-4b71-8797-80971f7b2b22",
                "route_id": "",
                "route_long_name": "",
                "route_short_name": "",
                "route_type": "",
                "trip_id": "",
                "headsign": "",
                "trip_begin": "",
                "trip_end": "",
                "days": [ "Mon", "Tue", "Wed", "Thu", "Fri" ],
                "stops": [
                    {
                        "id": "", 
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 1,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    },
                                        {
                        "id": "",
                        "stop_id": "",
                        "stop_name": "",
                        "stop_seq": 2,
                        "stop_lat": "",
                        "stop_long": "",
                        "arrival_time": "",
                        "departure_time": ""
                    }
                ]
            }
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get Location of Trip
##### *Authentication required*

Gets location of trip.

``GET /trips/:trip_id/location``

#### Response

``` json
{
    "success": true,
    "result": {
        "vehicle_id": "",
        "lat": "",
        "long": "",
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

May return error code 1005 if the trip is not active.

### Get All Subscriptions
##### *Authentication required*

Gets all subscriptions `auth_token` has access to.

``GET /subscriptions``

#### Response

``` json
{
    "success": true,
    "result": {
        "count": 2,
        "subscriptions": [
            {
                "id": "",
                "route_id": "",
                "trip_id": "",
                "stop_time": {
                    "stop_time_id": "",
                    "stop_id": "",
                    "stop_name": "",
                    "stop_code": "",
                    "arrival_time": "",
                    "departure_time": ""
                },
                "days": ["Mon", "Wed"],
                "date_created": "",
                "archived": false
            },
            {
                "id": "",
                "route_id": "",
                "trip_id": "",
                "stop_time": {
                    "stop_time_id": "",
                    "stop_id": "",
                    "stop_name": "",
                    "stop_code": "",
                    "arrival_time": "",
                    "departure_time": ""
                },
                "days": ["Mon", "Wed"],
                "date_created": "",
                "archived": true
            },
        ]
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get Individual Subscriptions
##### *Authentication required*

Gets single subscription

``GET /subscriptions/:subscription_id``

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "",
        "route_id": "",
        "trip_id": "",
        "stop_time": {
            "stop_time_id": "",
            "stop_id": "",
            "stop_name": "",
            "stop_code": "",
            "arrival_time": "",
            "departure_time": ""
        },
        "days": ["Mon", "Wed"],
        "date_created": "",
        "archived": false
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Create Subscription
##### *Authentication required*

Create a subscription

``POST /subscriptions``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
trip_id | string | n | Delay `trip_id`
stop_time_id | string | n | Delay `stop_time_id`
days | string[] | n | Array of three letter day codes for which the user wishes to subscribe to the trip for
notification_ids | uuid[] | y | ID of notification methods. If not present firebase cloud tokens used as default. 

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "",
        "route_id": "",
        "trip_id": "",
        "stop_time": {
            "stop_time_id": "",
            "stop_id": "",
            "stop_name": "",
            "stop_code": "",
            "arrival_time": "",
            "departure_time": ""
        },
        "days": ["Mon", "Wed"],
        "date_created": "",
        "archived": false
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

May return error code 1007.

### Modify Subscription
##### *Authentication required*

Modify an existing subscription. In order to stop a user receiving notifications for that subscription it must be archived. 

``PATCH /subscriptions/:subscription_id``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
trip_id | string | y | Delay `trip_id`
stop_time_id | string | y | Delay `stop_time_id`
days | string[] | y | Array of three letter day codes for which the user wishes to subscribe to the trip for
archived | boolean | y | Controls whether or not user gets notification

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "",
        "route_id": "",
        "trip_id": "",
        "stop_time": {
            "stop_time_id": "",
            "stop_id": "",
            "stop_name": "",
            "stop_code": "",
            "arrival_time": "",
            "departure_time": ""
        },
        "days": ["Mon", "Wed"],
        "date_created": "",
        "archived": false
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Delete Subscription

There is no delete method for subscriptions. Subscriptions will be automatically removed when they become invalid. 

### Create notification method
##### *Authentication required*

Create a new notification entry for user. By default when a notifiable event occurs and a subscription has no notification_ids setup delay will send the notification to all firebase cloud tokens setup on the account. 

``POST /notifications/``

#### Parameters

Name | Type | Optional | Description
-----|------|----------|-------------
type | char | n | Type of notification to be register. 
value | string | n | Value for notification. May be email, txt, or firebase cloud token
name | string | n | User friendly name for notification. For push notifications it is suggested to use the device name

##### Types

Character | Name | Description
----------|------|-------------
e | Email | Notify via email
t | Txt | Notify via txt message (note user must be premium in order to recieve txts)
p | Push | Notify via firebase cloud messaging push notification

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "f62a955d-7f06-422d-90fd-af2cd9092d31",
        "type": "e",
        "value": "bob@example.com",
        "name": "Work Email",
        "date_created": "1503020719"
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```
May return error code 1006.

### Get All Notification Methods
##### *Authentication required*

Returns all notification methods associated with a user account.

``GET /notifications/``

#### Response

``` json
{
    "success": true,
    "result": [
        {
            "id": "f62a955d-7f06-422d-90fd-af2cd9092d31",
            "type": "e",
            "value": "bob@example.com",
            "name": "Work Email",
            "date_created": "1503020719"
        },
        {
            "id": "d5104900-3029-4aa6-b4e6-b61d0f14d680",
            "type": "p",
            "value": "XXXXXXXXXXXX",
            "name": "Bob's iPhone",
            "date_created": "1503020719"
        },
        {
            "id": "b56b2b87-203d-41b7-bf45-d586daba00ed",
            "type": "t",
            "value": "5555555",
            "name": "Bob's Dumb Phone",
            "date_created": "1503020719"
        },
    ],
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Get Individual Notification Method
##### *Authentication required*

Returns a single notification by id.

``GET /notifications/:notification_id``

#### Response

``` json
{
    "success": true,
    "result": [
        {
            "id": "b56b2b87-203d-41b7-bf45-d586daba00ed",
            "type": "t",
            "value": "5555555",
            "name": "Bob's Dumb Phone",
            "date_created": "1503020719"
        }
    ],
    "errors": null,
    "meta": {
        // meta info
    }
}
```

### Delete Notification Method
##### *Authentication required*

Deletes given notification method. 

``DELETE /notifications/:notification_id``

#### Response

``` json
{
    "success": true,
    "result": {
        "id": "f62a955d-7f06-422d-90fd-af2cd9092d31",
        "deleted_on": "1503020858",
    },
    "errors": null,
    "meta": {
        // meta info
    }
}
```

## Response Codes

## HTTP

Code | Status | Description
-----|--------|-------------
200 | OK | Request successful
400 | Bad Request | Request invalid
401 | Unauthorized | User doesn't have permission
403 | Forbidden | Request not authenticated
404 | Not Found | Endpoint Not Found
405 | Method Not Allowed | Incorrect HTTP method
409 | Conflict | User already exists
415 | Unsupported Media Type | Request was invalid JSON
418 | I'm a tea pot
429 | Too Many Requests | `Auth_token` was rate limited

### Custom

Code | Description
-----|---------------------
1001 | Incorrect email or password
1002 | Returned if password is not set when email is or vice versa
1003 | Returned if `new_password`, `new_password_repeated` or `old_password` are not all set 
1004 | Bad behavior warning. Returned if caller is doing something that constitutes bad behavior
1005 | Trip not active
1006 | User not premium. Txt notification method cannot be added.
1007 | No notification id specified and no default firebase 

## Suggested Mobile Subscribe Flow

1. Ask user whether they would like to subscribe to a bus, train or ferry
2. Prompt the user to search 
3. Split user search into an array of each word
4. POST `route_type` and `params` to `/routes/search`
5. Display returned routes to user and let them select one
6. Call `/routes/:id/trips` on the selected route, using its Delay id
7. Prompt user for days they are interested in, save these as `days` for later
8. Display only trips matching the given days
9. Show users `trip_begin` and `trip_end` and prompt them to select the time frame their trip falls into
10. Display to the user all stops and stop times for the trip that matches the chosen time frame 
11. POST `stop_time_id` and `trip_id` along with `days` to `/subscriptions` to create a new subscription

## Note

This JSON API does not conform to a specific standard. It takes inspiration from [jsonapi.org](http://jsonapi.org) and [Cloudflare's](https://api.cloudflare.com/) apis. 

## Licence

![Creative Commons](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/)
