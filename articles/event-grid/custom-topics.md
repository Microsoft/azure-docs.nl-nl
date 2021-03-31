---
title: Aangepaste onderwerpen in Azure Event Grid
description: Beschrijft aangepaste onderwerpen in Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86113782"
---
# <a name="custom-topics-in-azure-event-grid"></a>Aangepaste onderwerpen in Azure Event Grid
Een event grid-onderwerp bevat een eind punt waar gebeurtenissen door de bron worden verzonden. De uitgever maakt het onderwerp Event grid en bepaalt of een gebeurtenis bron één onderwerp of meer dan één onderwerp nodig heeft. Er wordt een onderwerp gebruikt voor een verzameling van gerelateerde gebeurtenissen. Voor het reageren op bepaalde typen gebeurtenissen bepalen abonnees welke onderwerpen moeten worden geabonneerd.

**Aangepaste onderwerpen** zijn toepassingen en onderwerpen van derden. Wanneer u een aangepast onderwerp maakt of u er toegang toe krijgt, ziet u dat aangepaste onderwerp in uw abonnement. 

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit bij het bepalen van het aantal te maken onderwerpen. Voor grote oplossingen maakt u een **aangepast onderwerp** voor **elke categorie gerelateerde gebeurtenissen**. Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen kunt u de voor keur geven aan het verzenden van alle gebeurtenissen naar één onderwerp. Gebeurtenis abonnees kunnen filteren op de gewenste gebeurtenis typen.

## <a name="event-schema"></a>Gebeurtenisschema
Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een gedetailleerd overzicht van het gebeurtenis schema. Voor aangepaste onderwerpen wordt het **gegevens** object bepaald door de uitgever van de gebeurtenis. De gegevens op het hoogste niveau moeten dezelfde velden hebben als standaard, door de resource gedefinieerde gebeurtenissen.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

De volgende secties bevatten koppelingen naar zelf studies voor het maken van aangepaste onderwerpen met behulp van Azure Portal-, CLI-, Power shell-en Azure Resource Manager-sjablonen (ARM). 


## <a name="azure-portal-tutorials"></a>Azure Portal zelf studies
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met de Azure Portal](custom-event-quickstart-portal.md) | Laat zien hoe u de portal kunt gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstartgids: aangepaste gebeurtenissen naar Azure Queue-opslag routeren](custom-event-to-queue-storage.md) | Hierin wordt beschreven hoe u aangepaste gebeurtenissen naar een wachtrij opslag verzendt. |
| [Procedure: posten naar een aangepast onderwerp](post-to-custom-topic.md) | Laat zien hoe u een gebeurtenis op een aangepast onderwerp plaatst. |


## <a name="azure-cli-tutorials"></a>Zelf studies voor Azure CLI
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met Azure CLI](custom-event-quickstart.md) | Laat zien hoe u Azure CLI gebruikt om aangepaste gebeurtenissen te verzenden. |
| [Azure CLI: Event Grid aangepast onderwerp maken](./scripts/event-grid-cli-create-custom-topic.md)|Voorbeeld script voor het maken van een aangepast onderwerp. Het script haalt het eind punt en een sleutel op.|
| [Azure CLI: abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-cli-subscribe-custom-topic.md)|Voorbeeld script voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een webhook verzonden.|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell zelf studies
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstartgids: aangepaste gebeurtenissen maken en routeren met Azure PowerShell](custom-event-quickstart-powershell.md) | Laat zien hoe u Azure PowerShell kunt gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Power shell: Event Grid aangepast onderwerp maken](./scripts/event-grid-powershell-create-custom-topic.md)|Voorbeeld script voor het maken van een aangepast onderwerp. Het script haalt het eind punt en een sleutel op.|
| [Power shell: abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Voorbeeld script voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een webhook verzonden.|

## <a name="arm-template-tutorials"></a>Zelf studies voor ARM-sjablonen
|Titel  |Beschrijving  |
|---------|---------|
| [Resource Manager-sjabloon: aangepast onderwerp en webhook-eind punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Een resource manager-sjabloon voor het maken van een aangepast onderwerp en abonnement voor dat aangepaste onderwerp. Er worden gebeurtenissen naar een webhook verzonden. |
| [Resource Manager-sjabloon: aangepast onderwerp en Event Hubs eind punt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resource manager-sjabloon voor het maken van een abonnement voor een aangepast onderwerp. Er worden gebeurtenissen naar een Azure-Event Hubs verzonden. |

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Systeemonderwerpen](system-topics.md)
- [Domeinen](event-domains.md)