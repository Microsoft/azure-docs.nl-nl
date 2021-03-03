---
title: Hoek-invoeg toepassing voor Application Insights java script SDK
description: Een hoek-invoeg toepassing installeren en gebruiken voor Application Insights java script SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737015"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Hoek-invoeg toepassing voor Application Insights java script SDK

Met de hoek-invoeg toepassing voor de Application Insights java script SDK kunt u:

- Tracering van router wijzigingen

> [!WARNING]
> De hoek-invoeg toepassing is niet compatibel met ECMAScript 3 (ES3).

## <a name="getting-started"></a>Aan de slag

NPM-pakket installeren:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Basisgebruik

Stel een exemplaar van Application Insights in het onderdeel item in uw app in:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md) voor meer informatie over de Java script-SDK.
- [Hoek-invoeg toepassing op GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
