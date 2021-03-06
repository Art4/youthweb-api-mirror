---
title:  "Youthweb-API 0.5 mit mehr User-Daten"
categories: API
tags: [api, release]
summary: "Die neue Version der Youthweb-API zeigt jetzt viel mehr Daten in der Users-Resource an."
---
## Changelog

### Neu

- Die Resource `users/` liefert neue Attribute wie `first_name`, `last_name`, `email`, `birthday`, `created_at` und mehr.

## Beispiel

**Request**

```
GET https://youthweb.net/users/123456
Accept: application/vnd.api+json, application/vnd.api+json; net.youthweb.api.version=0.5
Content-Type: application/vnd.api+json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0NTgyMzE2MDAsImlzcyI6IkpOdlBnY3ROcEg1Y0s2UmMifQ.BOn0XFDDYa5iBHJb636A0C0m4sU5NO8SA_CPOVHoWNs
```

**Response**

```
Status: 200 OK
Accept: application/vnd.api+json, application/vnd.api+json; net.youthweb.api.version=0.5
Content-Type: application/vnd.api+json

{
    "data": {
        "type": "users",
        "id": "123456",
        "attributes": {
            "username": "JohnSmith",
            "first_name": "John",
            "last_name": "Smith",
            "email": "john_smith@example.org",
            "birthday": "1988-03-05",
            "created_at": "2006-01-01T21:00:00+01:00",
            "last_login": "2016-01-01T22:00:00+02:00",
            "zip": "12345",
            "city": "Jamestown",
            "description_jesus": "Lorem ipsum dolor sit amet",
            "description_job": "Lorem ipsum dolor sit amet",
            "description_hobbies": "Lorem ipsum dolor sit amet",
            "description_motto": "Lorem ipsum dolor sit amet",
            "picture_thumb_url": "https://youthweb.net/img/steckbriefe/default_pic_m.jpg",
            "picture_url": "https://youthweb.net/img/steckbriefe/default_pic_m.jpg"
        }
    }
}
```

{% include links.html %}
