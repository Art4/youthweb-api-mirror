---
title: OAuth2
keywords: Youthweb-API, Oauth2, Authorization
tags: [getting_started]
summary: "Die Youthweb-API verwendet OAuth2 zur Authorization"
sidebar: api_sidebar
permalink: api_general_oauth2.html
folder: api
---

Die meisten Resourcen benötigen eine Autorisierung. Dazu benötigt der Client ein Access-Token.

## Client registrieren

Als erstes solltest du deine App [hier](https://youthweb.net/settings/clients/new) registrieren, um eine `client_id` und ein `client_secret` zu erhalten. Wenn du deine App nicht registriest, erhält deine App nur auf sehr wenige Bereiche Zugriff; vergleichbar mit dem, was ein nicht eingeloggter Besucher auf Youthweb sehen kann.

Wenn du deine App registriert hast, kannst du folgende Methoden verwenden, um ein Access-Token zu erhalten.

## OAuth2 Authorization Code Grant

Wir haben das OAuth2 Authorization Code Grant umgesetzt, damit Apps nicht mit den Passwörtern der Nutzer arbeiten müssen.

### Webflow

Hier wird das Holen eines Request-Token und Access-Tokens durch eine App beschrieben. Der Prozess umfasst 5 Schritte.

#### 1. Client autorisieren

Der Client benötigt einen Request-Token und startet schickt den User (z.B. mithilfe eines WebViews) an die Url `https://youthweb.net/auth/authorize` und schickt im Querystring die folgenden Daten mit:

* `response_type` mit dem Wert `code`
* `client_id` mit der Client-ID
* `redirect_uri` mit der Client Redirect-URL. Dieser Wert ist optional und wenn nicht angegeben, wird die Redirect-URL genommen, die bei der Client-Registrierung angegeben wurde.
* `scope` mit einer (Leerzeichen getrennten) Liste an Scopes, siehe [hier][api_general_scopes].
* `state` mit einem CSRF Token. Dieser Wert ist optional, aber wird dringend empfohlen, um CSRF Angriffe zu verhindern. Der Wert wird beim Response wieder mitgegeben und der Client prüft, ob der Wert der selbe ist wie beim Request.

**Request**

```
GET https://youthweb.net/auth/authorize?response_type=code&client_id=JZuZLtoMgjWXnqJY&redirect_uri=https%3A%2F%2Fexample.com%2Fcallback&scope=user%3Aread&state=random_string
```

#### 2. User-Login

_Wenn der User eingeloggt ist, geht es weiter mit [Schritt 3](#3-anwendung-autorisieren)._

Wenn der User nicht eingeloggt ist, wird er zu einem Login-Formular weitergeleitet.

**Login**

```
GET https://youthweb.net/login?redirect=auth%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3DJZuZLtoMgjWXnqJY%26redirect_uri%3Dhttps%253A%252F%252Fexample.com%252Fcallback%26scope%3Duser%253Aread%26state%3Drandom_string
```

Nach dem erfolgreichen Login wird der User wieder zurück auf die ursprüngliche URL `https://youthweb.net/auth/authorize?response_type=code&client_id=JZuZLtoMgjWXnqJY&redirect_uri=https%3A%2F%2Fexample.com%2Fcallback&scope=user%3Aread&state=random_string` weitergeleitet.

#### 3. Anwendung autorisieren

_Wenn der User den Client bereits erlaubt hat, geht es weiter mit [Schritt 4](#4-redirect-url-mit-request-token-aufrufen)._

Der Youthweb-Server verarbeitet jetzt die Parameter aus dem Querystring. Wenn der User diesem Client noch keine Erlaubnis erteilt hat oder im Scope neue Berechtigungen angefragt werden, dann wird der User um Erlaubnis gefragt.

Wenn der User der Berechtigungs-Anfrage zustimmt, geht es weiter mit [Schritt 4](#4-redirect-url-mit-request-token-aufrufen).

#### 4. Redirect-URL mit Request-Token aufrufen

Wenn der User den Zugriff durch den Client erlaubt, erstellt der Server ein Authorization-Code und leitet auf die übergebene oder beim Client hinterlegte Redirect-URL um. Im Querystring werden dabei die folgenden Daten übertragen:

* `code` mit einem Authorization Code
* `state` mit dem Wert aus [Schritt 1](#1-client-autorisieren)

Der Client prüft dann, ob der `state`-Parameter den selben Wert wie beim Request in [Schritt 1](#1-client-autorisieren) hat. Wenn nicht, dann sollte der Client abbrechen und wieder mit Schritt 1 beginnen. Wer der `state` stimmt, dann ermittelt der Client den Code-Parameter aus der URL.

**Beispiel Response-URL**

```
GET https://example.org/callback?state=random_string&code=asdfbalkjflkj34ltkj34l5kgnw5twg...
```

{% include note.html content="Vergiss nicht, dass der `code` urlencoded ist. Du musst also auf jeden Fall `urldecode` anwenden, bevor du mit dem Code ein Access-Token anfragen kannst." %}

#### 5. Access-Token anfragen

Der Client kann nun den Code mit einem POST-Request an den Youthweb-Server senden und ein Access-Token anfragen. Hier benötigst du die `client_id` und `client_secret`, die du beim Registrieren deiner App erhalten hast.

```
POST https://youthweb.net/auth/access_token
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "JZuZLtoMgjWXnqJY",
    "client_secret": "GAbSLKO5fxvRZ2RfPKmeV116AeeR3eCe",
    "redirect_uri":"https://example.com/callback",
    "code":"<code_aus_url>asdfbalkjflkj34ltkj34l5kgnw5twg..."
}
```

Der Youthweb-Server prüft die Daten und wenn alles in Ordnung ist, dass wird der Response ein JSON-Object mit folgenden Werte enthalten:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "token_type": "Bearer",
    "expires_in": 3600,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE0NzA5OTg0MDEsImlzcyI6IkFsRXdNOGhhSVByWWZCMFMifQ.skOweG5QtefZE9ZR_p5HnS1aBGcG3t0D3v9Cx6nCSq0",
    "refresh_token": "PW1nNHOU7MwRMvglMdzZz..."
  }
}
```

Das `access_token` kannst du nun bei allen API-Requests im Header mitgeben.

Wenn das Access-Token abgelaufen ist, kann [Schritt 5](#5-access-token-anfragen) erneut durchgeführt werden, um ein neues Access-Token zu erhalten.

Wenn bei der Anfrage nach dem Access-Token ein Fehler auftritt (z.B. weil das Request-Token abgelaufen ist oder der User dem Client die Berechtigung entzogen hat), muss der Client wieder bei [Schritt 1](#1-client-autorisieren) beginnen.

{% include note.html content="Das mitgelieferte Refresh-Token kann noch nicht verwendet werden. Wir planen aber die Umsetzung des Refresh Token Grants." %}

{% include links.html %}
