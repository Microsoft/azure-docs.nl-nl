---
title: Implementeren in een bestaand virtueel netwerk
description: Hierin wordt beschreven hoe u gebruikers van uw beheerde toepassing in staat stelt om een bestaand virtueel netwerk te selecteren. Het virtuele netwerk kan zich buiten de beheerde toepassing bevinden.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84261287"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Bestaand virtueel netwerk gebruiken met Azure Managed Applications

Dit artikel laat u zien hoe u een door Azure beheerde toepassing kunt definiëren die kan worden geïntegreerd met een bestaand virtueel netwerk in het abonnement van de klant. Met de beheerde toepassing kan de Consumer bepalen of er een nieuw virtueel netwerk moet worden gemaakt of dat er een bestaande wordt gebruikt. Het bestaande virtuele netwerk kan zich buiten de beheerde resource groep bevinden.

## <a name="main-template"></a>Hoofd sjabloon

Laten we eerst eens kijken naar de **mainTemplate.jsin** het bestand. Hieronder ziet u de volledige sjabloon voor het implementeren van een virtuele machine en de bijbehorende resources. Later gaat u nauw keuriger kijken wat de onderdelen van de sjabloon zijn die zijn gerelateerd aan het gebruik van een bestaand virtueel netwerk.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

U ziet dat het virtuele netwerk [voorwaardelijk is geïmplementeerd](../templates/conditional-resource-deployment.md). De Consumer geeft een parameter waarde die aangeeft of een nieuw, bestaand virtueel netwerk moet worden gemaakt of gebruikt. Als de gebruiker een nieuw virtueel netwerk selecteert, wordt de bron geïmplementeerd. Anders wordt de resource overgeslagen tijdens de implementatie.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

De variabele voor de virtuele netwerk-ID heeft twee eigenschappen. De ene eigenschap retourneert de resource-ID wanneer er een nieuw virtueel netwerk wordt geïmplementeerd. De andere eigenschap retourneert de resource-ID wanneer een bestaand virtueel netwerk wordt gebruikt. De resource-ID voor het bestaande virtuele netwerk bevat de naam van de resource groep die het virtuele netwerk bevat.

De subnet-ID wordt geconstrueerd op basis van de waarde voor de ID van het virtuele netwerk. De waarde wordt gebruikt die overeenkomt met de geselecteerde gebruikers.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

De netwerk interface wordt ingesteld op de subnet-ID-variabele.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI-definitie

Nu gaan we kijken naar de **createUiDefinition.jsin** het bestand. Het hele bestand is:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Het bestand bevat een virtueel netwerk element.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Met dit element kan de consumer een nieuw of bestaand virtueel netwerk selecteren.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Nieuw of bestaand virtueel netwerk":::

In de uitvoer neemt u een waarde op die aangeeft of de consumer een nieuw of bestaand virtueel netwerk heeft geselecteerd. Er is ook een beheerde identiteits waarde.

> [!NOTE]
> De uitvoer waarde voor de beheerde identiteit moet **managedIdentity** zijn.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het maken van het definitie bestand van de gebruikers interface, Zie [CreateUiDefinition.jsvoor het maken van een door Azure beheerde toepassing](create-uidefinition-overview.md).
