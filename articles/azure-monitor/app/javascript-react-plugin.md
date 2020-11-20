---
title: Invoeg toepassing reageren voor Application Insights java script-SDK
description: De reageer-invoeg toepassing voor Application Insights java script SDK installeren en gebruiken.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981082"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Invoeg toepassing reageren voor Application Insights java script-SDK

De invoeg toepassing reageren voor de Application Insights java script-SDK, maakt het volgende mogelijk:

- Tracering van route wijzigingen
- Gebruiks statistieken van onderdelen reageren

## <a name="getting-started"></a>Aan de slag

NPM-pakket installeren:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Basisgebruik

Initialiseer een verbinding met Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Laat uw onderdeel teruglopen met de functie voor hogere onderdelen om Application Insights in te scha kelen:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Configuratie

| Naam    | Standaard | Beschrijving                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| geschiedenis | null    | Router geschiedenis reageren. Zie de [documentatie over het reagerende router pakket](https://reactrouter.com/web/api/history)voor meer informatie. Voor meer informatie over het openen van het geschiedenis object buiten de onderdelen raadpleegt u de [Veelgestelde vragen over de reagerende router](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Het bijhouden van onderdelen gebruiken

Als u verschillende reageer onderdelen wilt bijhouden, moet u de functie voor hogere onderdelen gebruiken `withAITracking` .

Hiermee wordt de tijd van de `ComponentDidMount` gebeurtenis gemeten via de `ComponentWillUnmount` gebeurtenis. Om dit nauw keuriger te maken, wordt de tijd waarop de gebruiker niet actief was, afgetrokken `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Als u deze waarde wilt weer geven in de Azure Portal u naar de Application Insights resource moet navigeren, selecteert u het tabblad "metrische gegevens" en configureert u de lege grafieken om de aangepaste metrische naam te tonen "reageren van het onderdeel tijd (seconden)", selecteert u aggregatie (Sum, AVG, enzovoort) van uw metrische waarde en past u Splits "onderdeel naam" toe.

![Scherm afbeelding van een grafiek met de aangepaste metrische tijd van het onderdeel reageren (seconden) "splitsen op" onderdeel naam "](./media/javascript-react-plugin/chart.png)

U kunt ook aangepaste query's uitvoeren om Application Insights gegevens te verdelen over het genereren van rapporten en visualisaties volgens uw vereisten. In de Azure Portal navigeert u naar de Application Insights resource, selecteert u ' analyse ' in het bovenste menu van het tabblad Overzicht en voert u de query uit.

![Scherm afbeelding van aangepaste metrische query resultaten.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Het kan Maxi maal tien minuten duren voordat nieuwe aangepaste metrische gegevens worden weer gegeven in de Azure-Portal.

## <a name="using-react-hooks"></a>Reagerende hooks gebruiken

Het [reageren van hooks](https://reactjs.org/docs/hooks-reference.html) is een benadering van de status en het levenscyclus beheer in een reagerende toepassing zonder afhankelijk te zijn van op klasse gebaseerde reagerende onderdelen. De Application Insights-invoeg toepassing reageert biedt een aantal hooks-integraties die op een vergelijk bare manier werken als de benadering van de hogere volg orde van componenten.

### <a name="using-react-context"></a>Reagerende context gebruiken

De reagerende hooks voor Application Insights zijn ontworpen voor gebruik van [reagerende context](https://reactjs.org/docs/context.html) als een insluitend aspect. Als u context wilt gebruiken, initialiseert u Application Insights als hierboven en importeert u vervolgens het context object:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Deze context provider maakt Application Insights beschikbaar als een `useContext` Hook binnen alle onderliggende onderdelen van het programma.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

De `useTrackMetric` Hook repliceert de functionaliteit van het `withAITracking` hogere onderdeel, zonder dat er een extra onderdeel aan de structuur van het onderdeel wordt toegevoegd. De Hook heeft twee argumenten: eerst is het Application Insights exemplaar (dat kan worden verkregen van de `useAppInsightsContext` Hook) en een id voor het onderdeel voor tracering (zoals de naam).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Het werkt als het hogere onderdeel, maar reageert op de levens cyclus van hooks in plaats van een levens cyclus van een onderdeel. De Hook moet expliciet worden verstrekt aan gebruikers gebeurtenissen als er sprake is van een nood zaak om op bepaalde interacties uit te voeren.

### `useTrackEvent`

De `useTrackEvent` Hook wordt gebruikt om alle aangepaste gebeurtenissen bij te houden die een toepassing mogelijk moet volgen, zoals het klikken op een knop of een andere API-aanroep. Er worden twee argumenten gebruikt: de eerste is het Application Insights exemplaar (dat kan worden verkregen van de `useAppInsightsContext` Hook) en een naam voor de gebeurtenis.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Wanneer de Hook wordt gebruikt, kan hieraan een nettolading worden gegeven om extra gegevens toe te voegen aan de gebeurtenis wanneer deze wordt opgeslagen in Application Insights.

## <a name="react-error-boundaries"></a>Fout grenzen voor reageren

[Fout grenzen](https://reactjs.org/docs/error-boundaries.html) bieden een manier om een uitzonde ring af te handelen wanneer deze zich in een reagerende toepassing voordoet. Wanneer deze fout optreedt, is het waarschijnlijk dat de uitzonde ring moet worden vastgelegd. De reactie-invoeg toepassing voor Application Insights biedt een fout grens component waarmee de fout automatisch wordt geregistreerd wanneer deze zich voordoet.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

De `AppInsightsErrorBoundary` vereiste twee eigenschappen moeten worden door gegeven, het exemplaar dat `ReactPlugin` voor de toepassing is gemaakt en een onderdeel dat moet worden weer gegeven wanneer er een fout optreedt. Wanneer er een niet-verwerkte fout optreedt, `trackException` wordt aangeroepen met de informatie die is verstrekt aan de fout grens en `onError` wordt het onderdeel weer gegeven.

## <a name="sample-app"></a>Voorbeeldapp

Bekijk de [Application Insights reageren](https://github.com/Azure-Samples/application-insights-react-demo)op de demo.

## <a name="next-steps"></a>Volgende stappen

- Zie de [Application Insights java script SDK-documentatie](javascript.md)voor meer informatie over de Java script-SDK.
- Zie het [overzicht van logboek query's](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie over de Kusto-query taal en het opvragen van gegevens in log Analytics.
