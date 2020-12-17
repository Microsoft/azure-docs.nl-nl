---
title: Bericht replicatie taken en-toepassingen-Azure Service Bus | Microsoft Docs
description: Dit artikel bevat een overzicht van het maken van berichten replicatie taken en-toepassingen met Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657407"
---
# <a name="message-replication-tasks-and-applications"></a>Bericht replicatie taken en-toepassingen

Zoals beschreven in het artikel [replicatie en interregionale Federatie](service-bus-federation-overview.md) , worden de replicatie van bericht reeksen tussen de paren van service bus entiteiten en tussen service bus en andere bericht bronnen en-doelen in het algemeen op Azure functions gelean.

[Azure functions](../azure-functions/functions-overview.md) is een schaal bare en betrouw bare uitvoerings omgeving voor het configureren en uitvoeren van serverloze toepassingen, waaronder [bericht replicatie en Federatie](service-bus-federation-overview.md) taken.

In dit overzicht vindt u meer informatie over de ingebouwde mogelijkheden van Azure Functions voor dergelijke toepassingen, over code blokken die u kunt aanpassen en wijzigen voor transformatie taken, en over het configureren van een Azure Functions toepassing, zodat deze in het ideale geval wordt geïntegreerd met Service Bus en andere Azure Messa ging-Services. Voor veel details verwijst dit artikel naar de Azure Functions documentatie.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
