---
title: UI-definitie maken die verwijst naar functies
description: Hierin worden de functies beschreven die moeten worden gebruikt bij het maken van UI-definities voor Azure Portal die naar andere objecten verwijzen.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096948"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition verwijst naar functies

De functies die moeten worden gebruikt bij het verwijzen naar uitvoer van de eigenschappen of context van een CreateUiDefinition-bestand.

## <a name="basics"></a>bewerkingen

Retourneert de uitvoer waarden van een element dat is gedefinieerd in de stap [basis beginselen](create-uidefinition-overview.md#basics) . Geef de naam van het element door als een para meter voor deze functie.

Gebruik de functie [stappen ()](#steps) om de uitvoer waarden van elementen in andere stappen op te halen.

In het volgende voor beeld wordt de uitvoer van het element met `clusterName` de naam in de stap basis beginselen geretourneerd:

```json
"[basics('clusterName')]"
```

De geretourneerde waarden variëren op basis van het type element dat wordt opgehaald.

## <a name="location"></a>location

Retourneert de locatie die is geselecteerd in de stap basis beginselen of de huidige context.

In het volgende voor beeld wordt een waarde als resultaat gegeven `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Retourneert Details over de resourceGroup die zijn geselecteerd in de stap basis beginselen of de huidige context.

Het volgende voor beeld:

```json
"[resourceGroup()]"
```

Retourneert de volgende eigenschappen:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

U kunt elke gewenste waarde met punt notatie ophalen.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>stappen

Hiermee worden de elementen van een opgegeven stap geretourneerd. Geef de naam van de stap door als een para meter voor deze functie. Vanuit de geretourneerde elementen kunt u bepaalde eigenschaps waarden ophalen.

Als u de uitvoer waarden van elementen in de stap basis beginselen wilt ophalen, gebruikt u de functie [Basics ()](#basics) .

In het volgende voor beeld wordt de stap met de naam geretourneerd `vmParameters` . In die stap is een element met de naam `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>abonnement

Retourneert eigenschappen voor het abonnement dat is geselecteerd in de stap basis beginselen of de huidige context.

Het volgende voor beeld:

```json
"[subscription()]"
```

Retourneert de volgende eigenschappen:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Volgende stappen

* Voor een inleiding tot het ontwikkelen van de portal interface raadpleegt u [CreateUiDefinition.jsop voor het maken van de Azure-app voor de beheerde toepassing](create-uidefinition-overview.md).
