---
title: Native reageren-invoeg toepassing voor Application Insights java script SDK
description: De native reageren-invoeg toepassing voor Application Insights java script SDK installeren en gebruiken.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593597"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Native reageren-invoeg toepassing voor Application Insights java script SDK

Met de native reageer-invoeg toepassing voor Application Insights java script SDK worden apparaatgegevens verzameld. deze invoeg toepassing verzamelt standaard automatisch de volgende gegevens:

- **Unieke apparaat-id** (ook bekend als installatie-id)
- **Naam van het apparaat model** (zoals iPhone X, Samsung Galaxy vouwen, Huawei P30 Pro, enzovoort)
- **Apparaattype** (bijvoorbeeld hoorn, Tablet, enz.)

## <a name="requirements"></a>Vereisten

U moet een versie >= 2.0.0 van gebruiken `@microsoft/applicationinsights-web` . Deze invoeg toepassing werkt alleen in reagerende systeem eigen apps. Het werkt niet met [apps met behulp van het](https://docs.expo.io/)weer geven van het Framework en werkt daarom niet bij het maken van een systeem eigen app voor reageren.

## <a name="getting-started"></a>Aan de slag

Installeer en koppel het pakket ' [reageren-systeem eigen-apparaat-info](https://www.npmjs.com/package/react-native-device-info) '. Blijf het `react-native-device-info` pakket up-to-date om de nieuwste apparaatnamen te verzamelen met uw app.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>De invoeg toepassing initialiseren

Als u deze invoeg toepassing wilt gebruiken, moet u de invoeg toepassing maken en toevoegen als `extension` aan uw bestaande Application Insights-exemplaar.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md)voor meer informatie over de Java script-SDK.
- Zie het [overzicht van logboek query's](../../azure-monitor/logs/log-query-overview.md)voor meer informatie over de Kusto-query taal en het opvragen van gegevens in log Analytics.
