---
title: Versionierung
keywords: Youthweb-API, Versionierung
tags: [getting_started]
summary: "Die Versionierung der Youthweb-API"
sidebar: api_sidebar
permalink: api_general_versions.html
folder: api
---

Die API wird immer weiter entwickelt und der aktuelle Stand wird hier festgehalten. Die Änderungen an der API werden während der Entwicklung nicht immer abwärtskompatibel sein, weswegen wir [eine Versionierung](http://semver.org/) eingeführt haben. Die angefragte Version MUSS im Request-Header angegeben werden:

`Accept: application/vnd.api+json, application/vnd.api+json; net.youthweb.api.version=0.13`

### Support

{% include note.html content="Jede Version wird für mindestens 1 Jahr unterstützt." %}

Nach dem Release einer neuen Version garantieren wir den Support der Version ohne Breaking Changes für mindestens 1 Jahr. Mindestens 6 Monaten nach dem Release und der Verfügbarkeit einer neueren Version wird eine Warnung in jedem Response angezeigt. Wenn der Support für eine Version abgelaufen ist, kann sie nicht mehr verwendet werden und Request zu dieser veralteten Version werden einen `406 Not Acceptable`-Error zurückgeben.

### Versions-Übersicht

| Version      | Status             | Veröffentlicht | Unterstützt bis           |
|--------------|--------------------|----------------|---------------------------|
| **0.13**     | :heavy_check_mark: | 2018-12-16     | mindestens 2019-12-16     |
| **0.12**     | :warning:          | 2017-07-16     | mindestens 2018-07-16     |
| **0.11**     | :warning:          | 2017-07-02     | mindestens 2018-07-02     |
| **0.10**     | :warning:          | 2017-06-18     | mindestens 2018-06-18     |
| **0.9**      | :warning:          | 2017-06-04     | mindestens 2018-06-04     |
| **0.8**      | :warning:          | 2017-05-21     | mindestens 2018-05-21     |
| 0.7          | :x:                | 2016-11-20     | Version 0.13 - 2019-12-16 |
| 0.6          | :x:                | 2016-10-23     | Version 0.13 - 2019-12-16 |
| 0.5          | :x:                | 2016-07-03     | Version 0.13 - 2019-12-16 |

#### Status-Legende

- :heavy_check_mark: **supported**: unterstützte Version
- :warning: **deprecated**: veraltete, aber unterstütze Version, die eine Warnung in jedem Response anzeigt
- :x: **unsupported**: nicht mehr unterstützte Version, die einen 406-Error zurückgibt

{% include links.html %}
