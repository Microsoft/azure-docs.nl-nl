---
title: Filters voor abonnementen instellen in Azure Service Bus | Microsoft Docs
description: In dit artikel vindt u voor beelden voor het definiëren van filters en acties op Azure Service Bus onderwerp-abonnementen.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654169"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Abonnements filters instellen (Azure Service Bus)
In dit artikel vindt u enkele voor beelden over het instellen van filters op Service Bus onderwerp-abonnementen. Zie [filters](topic-filters.md)voor conceptuele informatie over filters.

## <a name="filter-on-system-properties"></a>Filteren op systeem eigenschappen
Als u wilt verwijzen naar een systeem eigenschap in een filter, gebruikt u de volgende indeling: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filteren op bericht eigenschappen
Hier volgen de voor beelden van het gebruik van bericht eigenschappen in een filter. U kunt toegang krijgen tot bericht eigenschappen met `user.property-name` of alleen `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filteren op bericht eigenschappen met speciale tekens
Als de naam van de bericht eigenschap speciale tekens bevat, gebruikt u dubbele aanhalings teken ( `"` ) om de naam van de eigenschap op te sluiten. Als de naam van de eigenschap bijvoorbeeld is `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , gebruikt u de volgende syntaxis in het filter. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filteren op bericht eigenschappen met numerieke waarden
De volgende voor beelden ziet u hoe u eigenschappen kunt gebruiken met numerieke waarden in filters. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Op para meters gebaseerde filters
Hier volgen enkele voor beelden van het gebruik van filtering op basis van para meters. In deze voor beelden `DataTimeMp` is een bericht eigenschap van het type `DateTime` en `@dtParam` is een para meter die aan het filter wordt door gegeven als een `DateTime` object.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Gebruiken IN en niet IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Zie voor een C#-voor beeld [onderwerp filters voor beeld op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filter-using-correlationid"></a>Correlatie filter met behulp van CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Er worden berichten gefilterd met `CorrelationID` ingesteld op `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Correlatie filter met behulp van systeem-en gebruikers eigenschappen

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Het is gelijk aan: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende voorbeelden: 

- [.NET-eenvoudige zelf studie verzenden en ontvangen met filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-onderwerp filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-sjabloon](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)