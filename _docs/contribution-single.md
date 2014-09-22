---
layout: docs
title:  "Single contribution"
---
You can access a single contribution through three different endpoints.

**All users:**

``````
GET /api/projects/:project_id/data-groupings/all-contributions/contributions/:contribution_id/
``````

**Contributors:**

``````
GET /api/projects/:project_id/maps/data-groupings/my-contributions/contributions/:contribution_id/
``````

**Users that have been granted access to a data grouping:**

``````
GET /api/projects/:project_id/data-groupings/:grouping_id/contributions/:contribution_id/
``````

#### Request parameters

Parameter         | Type        | Description
------------------|-------------|--------------------------------------
`project_id`      | `Integer`   | Unique identifier for the project.
`grouping_id`     | `Integer`   | Optional. Unique identifier for the data grouping.
`contribution_id` | `Integer`   | Unique identifier for the contribution.

#### Example response

The response contains the [GeoJSON](http://geojson.org/geojson-spec.html) encoded [contribution](contribution-response.html).

{% highlight json %}
{
    "id": 2966,
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [
            -0.144415497779846,
            51.54671869005856
        ]
    },
    "properties": {
        "status": "active",
        "category": 40,
        "review_comment": null,
        "creator": {
            "id": 2,
            "display_name": "Oliver"
        },
        "updator": null,
        "created_at": "2014-09-19T15:51:32.804Z",
        "version": 1,
        "location": {
            "id": 2964,
            "name": null,
            "description": null,
            "created_at": "2014-09-19T15:51:32.790Z"
        },
        "attributes": {
            "child_friedly": false,
            "name": "The Grafton",
            "address": "20 Prince of Wales Rd, London NW5 3LG"
        }
    },
    "comments": [],
    "isowner": true,
    "category": {
        "id": 40,
        "name": "Pubs",
        "description": "",
        "status": "active",
        "fields": [
            {
                "id": 117,
                "name": "Name",
                "key": "name",
                "fieldtype": "TextField",
                "description": "",
                "status": "active",
                "required": true
            },
            {
                "id": 118,
                "name": "Address",
                "key": "address",
                "fieldtype": "TextField",
                "description": "",
                "status": "active",
                "required": false
            },
            {
                "id": 119,
                "name": "Child friedly",
                "key": "child_friedly",
                "fieldtype": "TrueFalseField",
                "description": "Would your take your kids?",
                "status": "active",
                "required": false
            }
        ],
        "colour": "#0033ff",
        "created_at": "2014-09-17T00:00:00Z"
    },
}
{% endhighlight %}