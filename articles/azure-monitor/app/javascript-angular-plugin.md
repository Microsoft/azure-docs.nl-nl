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
ms.openlocfilehash: 7ac83d0c43026b431370fab1d8c49aec1adf6659
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312719"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Hoek-invoeg toepassing voor Application Insights java script SDK

Met de hoek-invoeg toepassing voor de Application Insights java script SDK kunt u:

- Tracering van router wijzigingen
- Niet-onderschepte uitzonde ringen bijhouden

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

Voor het bijhouden van niet-onderschepte uitzonde ringen ApplicationinsightsAngularpluginErrorService in `app.module.ts` :

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md) voor meer informatie over de Java script-SDK.
- [Hoek-invoeg toepassing op GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
