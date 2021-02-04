---
title: Integratie beleid voor Azure API Management Dapr | Microsoft Docs
description: Meer informatie over Azure API Management-beleid voor interactie met Dapr micro Services-extensies.
author: vladvino
ms.author: vlvinogr
ms.date: 10/23/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: b8e253f75f56f961a24a441188b7a8e571622667
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99560228"
---
# <a name="api-management-dapr-integration-policies"></a>API Management-integratie beleid voor Dapr

Dit onderwerp bevat een Naslag informatie voor Dapr-integratie API Management-beleid. Dapr is een draag bare runtime voor het bouwen van stateless en stateful micro Services-toepassingen op basis van elke taal of elk Framework. De gemeen schappelijke micro service-patronen, zoals service detectie en aanroepen met de logica voor het maken van een nieuwe poging, worden in het geding en geabonneerd op minstens eenmaal bezorgings semantiek of pluggable bindings bronnen om de samen stelling met externe services te vereenvoudigen. Ga naar [dapr.io](https://dapr.io) voor gedetailleerde informatie en instructies over hoe u aan de slag kunt gaan met dapr. Zie [beleid in API Management](api-management-howto-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

> [!CAUTION]
> Beleids regels waarnaar in dit onderwerp wordt verwezen, zijn beschikbaar in de open bare preview-versie en zijn onderworpen aan [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Beleids regels waarnaar in dit onderwerp wordt verwezen, werken alleen in de [zelf-hostende versie van de API Management Gateway](self-hosted-gateway-overview.md) waarvoor Dapr-ondersteuning is ingeschakeld.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Ondersteuning voor Dapr inschakelen op de zelf-hostende gateway

Als u ondersteuning voor Dapr wilt inschakelen op de zelf-hostende gateway, voegt u de onderstaande [aantekeningen voor Dapr](https://github.com/dapr/docs/blob/master/README.md) toe aan de sjabloon voor de [Kubernetes-implementatie](how-to-deploy-self-hosted-gateway-kubernetes.md) waarbij "app-naam" wordt vervangen door een gewenste naam. Voltooi de procedure voor het instellen en gebruiken van API Management met Dapr is [hier](https://aka.ms/apim/dapr/walkthru)beschikbaar.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Integratiebeleid voor Gedistribueerde toepassingruntime (Dapr)

-  [Aanvraag verzenden naar een service](api-management-dapr-policies.md#invoke): maakt gebruik van Dapr-runtime om een Dapr-micro service te zoeken en betrouwbaar te communiceren. Zie de beschrijving in dit [Leesmij](https://github.com/dapr/docs/blob/master/README.md#service-invocation) -bestand voor meer informatie over het aanroepen van de service in Dapr.
-  [Bericht verzenden naar pub/sub-onderwerp](api-management-dapr-policies.md#pubsub): maakt gebruik van Dapr runtime om een bericht te publiceren naar een onderwerp over publiceren/abonneren. Zie de beschrijving in dit [Leesmij](https://github.com/dapr/docs/blob/master/README.md) -bestand voor meer informatie over het publiceren/abonneren van berichten in Dapr.
-  [Uitvoer binding activeren](api-management-dapr-policies.md#bind): maakt gebruik van Dapr runtime om een extern systeem via een uitvoer binding aan te roepen. Zie de beschrijving in dit [Leesmij](https://github.com/dapr/docs/blob/master/README.md) -bestand voor meer informatie over bindingen in Dapr.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Aanvraag verzenden naar een service

Met dit beleid wordt de doel-URL ingesteld voor de huidige aanvraag om `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` sjabloon parameters te vervangen door waarden die zijn opgegeven in de-beleids verklaring.

In het beleid wordt ervan uitgegaan dat Dapr wordt uitgevoerd in een zijspan container in dezelfde pod als de gateway. Wanneer de aanvraag wordt ontvangen, voert Dapr runtime service detectie en werkelijke aanroep uit, inclusief mogelijke protocol omzetting tussen HTTP en gRPC, nieuwe pogingen, gedistribueerde tracering en fout afhandeling.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u het aanroepen van de methode ' back ' op de micro service met de naam ' echo '. Het `set-backend-service` beleid stelt de doel-URL in. Het `forward-request` beleid verzendt de aanvraag naar de Dapr-runtime, waardoor deze naar de micro service wordt geleverd.

Het `forward-request` beleid wordt hier weer gegeven voor duidelijkheid. Het beleid wordt doorgaans overgenomen van het globale bereik via het `base` sleutel woord.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementen

| Element             | Beschrijving  | Vereist |
|---------------------|--------------|----------|
| set-back-end-service | Hoofd element | Yes      |

### <a name="attributes"></a>Kenmerken

| Kenmerk        | Beschrijving                     | Vereist | Standaard |
|------------------|---------------------------------|----------|---------|
| back-end-id       | Moet worden ingesteld op ' dapr '           | Yes      | N.v.t.     |
| dapr-app-id      | De naam van de doel-micro service. Wordt toegewezen aan de [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) -para meter in Dapr.| Yes | N.v.t. |
| dapr-methode      | De naam van de methode of een URL die moet worden aangeroepen voor de doel-micro service. Wordt toegewezen aan de para meter [method-name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) in Dapr.| Yes | N.v.t. |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Bericht verzenden naar pub/sub-onderwerp

Dit beleid geeft API Management Gateway om een bericht te verzenden naar een Dapr Publish/Subscriber-onderwerp. Het beleid zorgt ervoor dat door een HTTP POST-aanvraag te maken voor het `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` vervangen van sjabloon parameters en het toevoegen van inhoud die is opgegeven in de beleids verklaring.

In het beleid wordt ervan uitgegaan dat Dapr runtime wordt uitgevoerd in een zijspan container in dezelfde pod als de gateway. Dapr runtime implementeert de pub/sub-semantiek.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt gedemonstreerd dat de hoofd tekst van de huidige aanvraag wordt verzonden naar het [onderwerp](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) ' nieuw ' van het [onderdeel](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)' orders ' van het pub-element ' order '. Het antwoord dat is ontvangen van de Dapr-runtime wordt opgeslagen in de vermelding ' Dapr-Response ' van de verzameling Varia bles in het [context](api-management-policy-expressions.md#ContextVariables) -object.

Als Dapr runtime het doel onderwerp niet kan vinden, bijvoorbeeld reageert op een fout, wordt de sectie ' op de fout ' geactiveerd. Het antwoord dat is ontvangen van de Dapr-runtime wordt geretourneerd naar de aanroeper Verbatim. Anders wordt standaard `200 OK` antwoord geretourneerd.

De sectie back-end is leeg en de aanvraag wordt niet doorgestuurd naar de back-end.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementen

| Element             | Beschrijving  | Vereist |
|---------------------|--------------|----------|
| publiceren naar dapr     | Hoofd element | Yes      |

### <a name="attributes"></a>Kenmerken

| Kenmerk        | Beschrijving                     | Vereist | Standaard |
|------------------|---------------------------------|----------|---------|
| pubsub-naam      | De naam van het doel-PubSub-onderdeel. Wordt toegewezen aan de para meter [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) in Dapr. Als dat niet het geval is, moet de waarde van het __onderwerp__ worden opgegeven in de vorm van `pubsub-name/topic-name` .    | No       | Geen    |
| onderwerp            | De naam van het onderwerp. Wordt toegewezen aan de [onderwerp](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) -para meter in Dapr.               | Yes      | N.v.t.     |
| negeren-fout     | Als deze instelling is ingesteld op `true` , wordt het beleid niet geactiveerd om de sectie ["On-Error"](api-management-error-handling-policies.md) te activeren bij het ontvangen van een fout vanuit Dapr runtime | No | `false` |
| reactie-variabele-naam | Naam van de verzamelings vermelding van de [variabelen](api-management-policy-expressions.md#ContextVariables) die moet worden gebruikt voor het opslaan van de reactie van Dapr runtime | No | Geen |
| timeout | Tijd (in seconden) die moet worden gewacht voordat Dapr runtime reageert. U kunt een waarde tussen 1 en 240 seconden opgeven. | No | 5 |
| sjabloon | De sjabloon-engine die moet worden gebruikt voor het transformeren van de bericht inhoud. "Liquid" is de enige ondersteunde waarde. | No | Geen |
| inhouds type | Het type van de bericht inhoud. ' application/json ' is de enige ondersteunde waarde. | No | Geen |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** inkomend, uitgaand, op fouten
- **Beleids bereik:** alle bereiken

## <a name="trigger-output-binding"></a><a name="bind"></a> Uitvoer binding activeren

Dit beleid geeft API Management Gateway om een uitgaande Dapr- [binding](https://github.com/dapr/docs/blob/master/README.md)te activeren. Het beleid zorgt dat door een HTTP POST-aanvraag te maken voor het `http://localhost:3500/v1.0/bindings/{{bind-name}}` vervangen van de sjabloon parameter en het toevoegen van inhoud die is opgegeven in de beleids verklaring.

In het beleid wordt ervan uitgegaan dat Dapr runtime wordt uitgevoerd in een zijspan container in dezelfde pod als de gateway. Dapr runtime is verantwoordelijk voor het aanroepen van de externe resource die wordt vertegenwoordigd door de binding.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt het activeren van een uitgaande binding met de naam "externe systemen" aangeduid met de bewerking name "Create", meta gegevens die bestaan uit twee sleutel/waarde-items met de naam source en client-IP, en de hoofd tekst van de oorspronkelijke aanvraag. Het antwoord dat is ontvangen van de Dapr-runtime wordt vastgelegd in de vermelding ' binding-Response ' van de verzameling Varia bles in het [context](api-management-policy-expressions.md#ContextVariables) -object.

Als Dapr runtime om een of andere reden mislukt en reageert met een fout, wordt de sectie ' aan de fout ' geactiveerd en wordt het antwoord dat is ontvangen van de Dapr-runtime geretourneerd naar de aanroeper Verbatim. Anders wordt standaard `200 OK` antwoord geretourneerd.

De sectie back-end is leeg en de aanvraag wordt niet doorgestuurd naar de back-end.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementen

| Element             | Beschrijving  | Vereist |
|---------------------|--------------|----------|
| invoke-dapr-binding | Hoofd element | Yes      |
| metagegevens            | Bindende specifieke meta gegevens in de vorm van sleutel/waarde-paren. Wordt toegewezen aan de [meta gegevens](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) eigenschap in Dapr. | No |
| gegevens            | De inhoud van het bericht. Wordt toegewezen aan de eigenschap [Data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) in Dapr. | No |


### <a name="attributes"></a>Kenmerken

| Kenmerk        | Beschrijving                     | Vereist | Standaard |
|------------------|---------------------------------|----------|---------|
| naam            | Doel binding naam. Moet overeenkomen met de naam van de bindingen die zijn [gedefinieerd](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) in Dapr.           | Yes      | N.v.t.     |
| bewerking       | Doel bewerkings naam (binding specifiek). Wordt toegewezen aan de [bewerkings](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) eigenschap in Dapr. | No | Geen |
| negeren-fout     | Als deze instelling is ingesteld op `true` , wordt het beleid niet geactiveerd om de sectie ["On-Error"](api-management-error-handling-policies.md) te activeren bij het ontvangen van een fout vanuit Dapr runtime | No | `false` |
| reactie-variabele-naam | Naam van de verzamelings vermelding van de [variabelen](api-management-policy-expressions.md#ContextVariables) die moet worden gebruikt voor het opslaan van de reactie van Dapr runtime | No | Geen |
| timeout | Tijd (in seconden) die moet worden gewacht voordat Dapr runtime reageert. U kunt een waarde tussen 1 en 240 seconden opgeven. | No | 5 |
| sjabloon | De sjabloon-engine die moet worden gebruikt voor het transformeren van de bericht inhoud. "Liquid" is de enige ondersteunde waarde. | No | Geen |
| inhouds type | Het type van de bericht inhoud. ' application/json ' is de enige ondersteunde waarde. | No | Geen |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** inkomend, uitgaand, op fouten
- **Beleids bereik:** alle bereiken
