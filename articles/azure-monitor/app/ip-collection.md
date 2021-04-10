---
title: Verzameling van Azure-toepassing Insights-IP-adressen | Microsoft Docs
description: Meer informatie over hoe IP-adressen en geolocatie worden verwerkt met Azure-toepassing Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584082"
---
# <a name="geolocation-and-ip-address-handling"></a>Verwerking van geolocatie en IP-adres

In dit artikel wordt uitgelegd hoe geolocatie lookup en IP-adres afhandeling werkt in Application Insights samen met het wijzigen van het standaard gedrag.

## <a name="default-behavior"></a>Standaardgedrag

IP-adressen worden standaard tijdelijk verzameld, maar niet opgeslagen in Application Insights. Het basisproces verloopt als volgt:

Wanneer telemetrie naar Azure wordt verzonden, wordt het IP-adres gebruikt voor het uitvoeren van een geolocatie lookup met [GeoLite2 van Maxmind](https://dev.maxmind.com/geoip/geoip2/geolite2/). De resultaten van deze zoek actie worden gebruikt voor het vullen van de velden `client_City` , `client_StateOrProvince` en `client_CountryOrRegion` . Het adres wordt vervolgens verwijderd en `0.0.0.0` naar het `client_IP` veld geschreven.

* Browser-telemetrie: het IP-adres van de afzender wordt tijdelijk verzameld. Het IP-adres wordt berekend door het opname-eind punt.
* Server-telemetrie: de Application Insights telemetrie-module verzamelt tijdelijk het client-IP-adres. Het IP-adres wordt niet lokaal verzameld wanneer de `X-Forwarded-For` header is ingesteld.

Dit gedrag is inherent aan het ontwerp om onnodig verzamelen van persoons gegevens te voor komen. Als dat mogelijk is, kunt u het verzamelen van persoons gegevens het beste vermijden. 

## <a name="overriding-default-behavior"></a>Standaard gedrag negeren

De standaard waarde is het verzamelen van IP-adressen. We bieden nog steeds de flexibiliteit om dit gedrag te negeren. We raden echter aan te verifiëren dat de verzameling geen nalevings vereisten of lokale voor Schriften onderbreekt. 

Raadpleeg de [richt lijnen voor persoonlijke gegevens](../logs/personal-data-mgmt.md)voor meer informatie over het afhandelen van persoonlijke gegevens in Application Insights.

## <a name="storing-ip-address-data"></a>IP-adres gegevens opslaan

Als u IP-verzameling en-opslag wilt inschakelen, `DisableIpMasking` moet de eigenschap van het onderdeel Application Insights worden ingesteld op `true` . Deze eigenschap kan worden ingesteld via Azure Resource Manager sjablonen of door de REST API aan te roepen. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Als u het gedrag voor een enkele Application Insights resource alleen hoeft te wijzigen, gebruikt u de Azure Portal. 

1. Ga naar de Application Insights resource > **Automation**-  >  **export sjabloon** 

2. Selecteer **Implementeren**

    ![Knop met woord ' implementeren ' gemarkeerd in rood](media/ip-collection/deploy.png)

3. Selecteer **sjabloon bewerken**.

    ![Knop met het woord ' bewerken ' gemarkeerd in rood](media/ip-collection/edit-template.png)

4. Breng de volgende wijzigingen aan in de JSON voor uw resource en selecteer vervolgens **Opslaan**:

    ![In de scherm afbeelding wordt een komma toegevoegd na ' IbizaAIExtension ' en een nieuwe regel toegevoegd onder ' DisableIpMasking ': True](media/ip-collection/save.png)

    > [!WARNING]
    > Als er een fout optreedt met de melding: **_de resource groep bevindt zich op een locatie die niet wordt ondersteund door een of meer resources in de sjabloon. Kies een andere resource groep._** Selecteer tijdelijk een andere resource groep in de vervolg keuzelijst en selecteer vervolgens de oorspronkelijke resource groep om de fout op te lossen.

5. Selecteer **Ik ga akkoord** met  >  **aankopen**. 

    ![Selectie vakje met de woorden "Ik ga akkoord met de bovenstaande voor waarden" gemarkeerd in rood boven een knop met het woord "aankoop" rood gemarkeerd.](media/ip-collection/purchase.png)

    In dit geval wordt er niets nieuwe gekocht. De configuratie van de bestaande Application Insights resource wordt alleen bijgewerkt.

6. Zodra de implementatie is voltooid, worden er nieuwe telemetriegegevens vastgelegd.

    Als u de sjabloon opnieuw selecteert en bewerkt, ziet u alleen de standaard sjabloon zonder de zojuist toegevoegde eigenschap. Als u geen IP-adres gegevens ziet en wilt bevestigen dat deze `"DisableIpMasking": true` is ingesteld, voert u de volgende Power shell uit: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Er wordt een lijst met eigenschappen geretourneerd als resultaat. Een van de eigenschappen moet worden gelezen `DisableIpMasking: true` . Als u de Power shell uitvoert voordat u de nieuwe eigenschap implementeert met Azure Resource Manager, bestaat de eigenschap niet.

### <a name="rest-api"></a>Rest API

De nettolading van de [rest-API](/rest/api/azure/) voor het maken van dezelfde wijzigingen is als volgt:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Initialisatie functie voor telemetrie

Als u een flexibeler alternatief nodig hebt dan `DisableIpMasking` , kunt u een [initialisatie functie voor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) gebruiken om het IP-adres geheel of gedeeltelijk naar een aangepast veld te kopiëren. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Als u geen toegang hebt tot `ISupportProperties` , controleert u of u de laatste stabiele versie van de Application INSIGHTS SDK uitvoert. `ISupportProperties` zijn bedoeld voor hoge kardinaliteit waarden, maar `GlobalProperties` zijn geschikter voor lage kardinaliteit waarden, zoals regio naam, omgevings naam, enzovoort. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>De initialisatie functie voor telemetrie voor ASP.NET inschakelen

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Initialisatie functie voor telemetrie inschakelen voor ASP.NET Core

U kunt de initialisatie functie ASP.NET Core voor telemetrie op dezelfde manier maken als ASP.NET, maar om de initialisatie functie in te scha kelen, gebruikt u het volgende voor beeld om te verwijzen:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript aan de clientzijde](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript aan de clientzijde

In tegens telling tot de Sdk's aan de server zijde, wordt het IP-adres niet berekend door de Java script SDK aan de client zijde. Standaard wordt de berekening van IP-adressen voor telemetrie aan de client zijde uitgevoerd op het opname-eind punt in Azure. 

Als u het IP-adres rechtstreeks aan de client zijde wilt berekenen, moet u uw eigen aangepaste logica toevoegen en het resultaat gebruiken om het label in te stellen `ai.location.ip` . Wanneer `ai.location.ip` is ingesteld, wordt de berekening van het IP-adres niet uitgevoerd door het opname-eind punt en wordt het opgegeven IP-adres gebruikt voor het zoeken naar geolocatie. In dit scenario wordt het IP-adres standaard nog steeds nul. 

Als u wilt dat het volledige IP-adres wordt berekend op basis van uw aangepaste logica, kunt u een initialisatie functie voor telemetrie gebruiken waarmee de IP-adres gegevens die u hebt ingevoerd, `ai.location.ip` worden gekopieerd naar een afzonderlijk aangepast veld. Maar in tegens telling tot de Sdk's aan de server zijde, zonder afhankelijk te zijn van bibliotheken van derden of uw eigen aangepaste verzamelings logica, wordt het adres niet voor u berekend door de SDK aan de client zijde.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Als gegevens aan de client zijde een proxy passeren voordat ze naar het opname-eind punt worden doorgestuurd, kan de IP-adres berekening het IP-adres van de proxy en niet de client weer geven. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>De resultaten van de initialisatie functie voor telemetrie weer geven

Als u nieuw verkeer naar uw site verzendt en een paar minuten wacht. U kunt vervolgens een query uitvoeren om te bevestigen dat de verzameling werkt:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nieuw verzamelde IP-adressen worden weer gegeven in de `customDimensions_client-ip` kolom. De standaard `client-ip` kolom bevat nog steeds vier octetten van nul. 

Als er wordt getest van localhost en de waarde voor `customDimensions_client-ip` is `::1` , wordt deze waarde verwacht gedrag. `::1` Hiermee wordt het loop back-adres in IPv6 aangeduid. Dit is gelijk aan `127.0.0.1` in IPv4.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [verzamelen van persoonlijke gegevens](../logs/personal-data-mgmt.md) in Application Insights.

* Meer informatie over hoe [IP-adres verzameling](https://apmtips.com/posts/2016-07-05-client-ip-address/) in Application Insights werkt. (In dit artikel is een oudere externe blog post geschreven door een van onze technici. Het huidige standaard gedrag waarbij het IP-adres wordt vastgelegd `0.0.0.0` , wordt voorgezet, maar het gaat in op meer gedetailleerde informatie over de mechanismen van de ingebouwde `ClientIpHeaderTelemetryInitializer` .)
