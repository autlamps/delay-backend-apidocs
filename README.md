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
If you specify an email, you must also specify a password. If a name is specified an 
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
    "success": true,
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

May return error code 1000, 1001.

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
    "errors": null,
    "result": {
        "route": {
            "id": "24f11799-6b9a-406f-927a-bf6e69add608",
            "gtfs_id": "81352-20171013114012_v59.18",
            "agency_id": "0eb30a1e-cc9d-4c09-b6d3-e7d23a08efe5",
            "short_name": "813",
            "long_name": "Takapuna Grammar To Takapuna",
            "route_type": "3"
        },
        "trips": [
            {
                "id": "6098e451-4d0e-4192-8557-91d106487bd2",
                "route_id": "24f11799-6b9a-406f-927a-bf6e69add608",
                "service_id": "f26e1b07-c6aa-4366-8ed0-96b2eb7ca23b",
                "gtfsid": "18138100119-20171013114012_v59.18",
                "headsign": "Schools",
                "calendar": {
                    "monday": true,
                    "tuesday": true,
                    "wednesday": true,
                    "thursday": true,
                    "friday": true,
                    "saturday": false,
                    "sunday": false,
                    "start_time": "03:05:04",
                    "end": "03:16:04"
                }
            },
            {
                "id": "032c8617-4e08-454c-89b2-5cfdc9ae91ee",
                "route_id": "24f11799-6b9a-406f-927a-bf6e69add608",
                "service_id": "6dc8398b-2fd9-4f6b-8011-f6fc151c6dca",
                "gtfsid": "18138100116-20171013114012_v59.18",
                "headsign": "Schools",
                "calendar": {
                    "monday": true,
                    "tuesday": true,
                    "wednesday": true,
                    "thursday": true,
                    "friday": true,
                    "saturday": false,
                    "sunday": false,
                    "start_time": "03:04:04",
                    "end": "03:15:04"
                }
            }
        ]
    },
    "meta": {
        "version": "Alpha",
        "by": "Izaac Crooke, Dhayrin Colbert, Dominic Porter, Hayden Woodhead"
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

### Get StopTimes for Trip
##### *Authentication required*

Gets all stoptimes for a given trip

``GET /trips/:trip_id/stoptimes``

#### Response
``` json
{
    "success": true,
    "errors": null,
    "result": {
        "count": 2,
        "stop_time": [
            {
                "id": "9b09750a-f93b-409a-bb8f-1b2fab6c8578",
                "trip_id": "6098e451-4d0e-4192-8557-91d106487bd2",
                "stop_sequence": 1,
                "stop_info": {
                    "id": "35688b3d-393c-4b26-93b8-7669e5d50ad5",
                    "name": "257 Lake Rd",
                    "lat": -36.8004,
                    "lon": 174.78639
                },
                "departure": "15:26:00",
                "arrival": "15:26:00"
            },
            {
                "id": "dc0b83b7-6cec-4a07-a487-fe6f3d58834a",
                "trip_id": "6098e451-4d0e-4192-8557-91d106487bd2",
                "stop_sequence": 2,
                "stop_info": {
                    "id": "f4afd7ff-0443-4825-9a75-6d9460123677",
                    "name": "293 Lake Rd",
                    "lat": -36.79806,
                    "lon": 174.78421
                },
                "departure": "15:27:52",
                "arrival": "15:27:52"
            }
        ]
    },
    "meta": {
        "version": "Alpha",
        "by": "Izaac Crooke, Dhayrin Colbert, Dominic Porter, Hayden Woodhead"
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
    "errors": null,
    "result": [
        {
            "id": "d06cb68d-478f-49f7-aa9a-ef29d50b41a0",
            "trip_id": "7450f9b7-cefa-4e6e-a9fe-da04978b6f08",
            "user_id": "b814640b-be37-47e0-bd1b-25770dad9d36",
            "archived": false,
            "monday": true,
            "tuesday": true,
            "wednesday": false,
            "thursday": false,
            "friday": false,
            "saturday": false,
            "sunday": false,
            "notification_ids": [
                "74d4a530-be90-487e-bca2-5aebeb0bcec0"
            ],
            "stop_time": {
                "id": "eaf38dc3-9c02-4682-baae-562046793022",
                "trip_id": "7450f9b7-cefa-4e6e-a9fe-da04978b6f08",
                "stop_sequence": 12,
                "stop_info": {
                    "id": "d1347aec-b67a-4b4b-804e-8217a5666c7c",
                    "name": "60 King George Ave",
                    "lat": -36.88885,
                    "lon": 174.76965
                },
                "departure": "14:51:10",
                "arrival": "14:51:10"
            },
            "created": 1509328705
        },
        {
            "id": "cedd2951-04d8-4466-83b3-21f0573d0fb0",
            "trip_id": "7450f9b7-cefa-4e6e-a9fe-da04978b6f08",
            "user_id": "b814640b-be37-47e0-bd1b-25770dad9d36",
            "archived": false,
            "monday": true,
            "tuesday": true,
            "wednesday": false,
            "thursday": false,
            "friday": false,
            "saturday": false,
            "sunday": false,
            "notification_ids": [
                "74d4a530-be90-487e-bca2-5aebeb0bcec0"
            ],
            "stop_time": {
                "id": "eaf38dc3-9c02-4682-baae-562046793022",
                "trip_id": "7450f9b7-cefa-4e6e-a9fe-da04978b6f08",
                "stop_sequence": 12,
                "stop_info": {
                    "id": "d1347aec-b67a-4b4b-804e-8217a5666c7c",
                    "name": "60 King George Ave",
                    "lat": -36.88885,
                    "lon": 174.76965
                },
                "departure": "14:51:10",
                "arrival": "14:51:10"
            },
            "created": 1509329133
        }
    ],
    "meta": {
        "version": "Alpha",
        "by": "Izaac Crooke, Dhayrin Colbert, Dominic Porter, Hayden Woodhead"
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
stoptime_id | string | n | Delay `stoptime_id`
days | string[] | n | Array of three letter day codes for which the user wishes to subscribe to the trip for
notification_ids | uuid[] | n | ID of notification methods for which the user wishes to receive delay notifications for  

#### Response

``` json

    "success": true,
    "errors": null,
    "result": {
        "id": "3068dcec-d5d8-4562-90df-f62884519517",
        "trip_id": "30f5831b-f35a-4f3b-bcf2-edb786b70ea8",
        "user_id": "0bd4ae69-d3af-491c-a11a-ccb7b086871e",
        "archived": false,
        "monday": true,
        "tuesday": false,
        "wednesday": true,
        "thursday": false,
        "friday": false,
        "saturday": false,
        "sunday": false,
        "notification_ids": [
            "22fbb1ba-7556-4b3b-a499-d7492e637a60"
        ],
        "stop_time": {
            "id": "1c35e020-9e4c-44d7-8c77-0638de05d406",
            "trip_id": "0f8fb9bd-db5e-4162-a7d9-0d41fb1049d8",
            "stop_sequence": 21,
            "stop_info": {
                "id": "4ba949f9-cc5b-488d-a092-ad9b79e37091",
                "name": "1392 Whangaparaoa Rd",
                "lat": -36.60359,
                "lon": 174.80204
            },
            "departure": "17:21:46",
            "arrival": "17:21:46"
        }
    },
    "meta": {
        "version": "Alpha",
        "by": "Izaac Crooke, Dhayrin Colbert, Dominic Porter, Hayden Woodhead"
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
1000 | Email doesn't match any registered account
1001 | Incorrect email or password
1002 | Returned if password is not set when email is or vice versa
1003 | Returned if `new_password`, `new_password_repeated` or `old_password` are not all set 
1004 | Bad behavior warning. Returned if caller is doing something that constitutes bad behavior
1005 | Trip not active
1006 | User not premium. Txt notification method cannot be added.
1007 | No notification id specified

## Suggested Mobile Subscribe Flow

1. Call `/routes` to get all routes
2. Prompt user to search and limit by short name, long name and [route type](https://developers.google.com/transit/gtfs/reference/#routestxt)
3. Call `/routes/:route_id/trips` with the users selected route
4. Display selected trips to users. Highly recommend showing the start time and end time to allow user to narrow down selection.
5. Call `/trips/:trip_id/stoptimes` on user selected trip
6. Display stoptimes to user 
7. On selection ask which days they wish to subscribe to
8. Post to `/subscriptions` as per documentation

## Note

This JSON API does not conform to a specific standard. It takes inspiration from [jsonapi.org](http://jsonapi.org) and [Cloudflare's](https://api.cloudflare.com/) apis. 

## Licence

![Creative Commons](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/)
