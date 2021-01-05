---
title: Overzicht van IT Service Management-connector
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93b6160b495b609e23e10b3f709d130792067423
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803779"
---
# <a name="it-service-management-connector-overview"></a>Overzicht van IT Service Management-connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Met IT Service Management-connector (ITSMC) kunt u Azure verbinden met een ondersteund ITSM-product of-service (IT Service Management).

Azure-Services zoals Azure Log Analytics en Azure Monitor bieden hulp middelen voor het detecteren, analyseren en oplossen van problemen met uw Azure-en niet-Azure-resources. Maar de werk items die betrekking hebben op een probleem bevinden zich doorgaans in een ITSM-product of-service. ITSMC biedt een bidirectionele verbinding tussen Azure-en ITSM-hulpprogram ma's die u helpen problemen sneller op te lossen.

## <a name="configuration-steps"></a>Configuratiestappen

ITSMC ondersteunt verbindingen met de volgende ITSM-hulpprogram ma's:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> We stellen onze klanten van Cher well en Provance voor om de [actie webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) te gebruiken voor Cher well en Provance eind punt als een andere oplossing voor de integratie.

Met ITSMC kunt u het volgende doen:

-  Maak werk items in uw ITSM-hulp programma, op basis van uw Azure-waarschuwingen (metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en Log Analytics waarschuwingen).
-  U kunt eventueel uw incident synchroniseren en gegevens van uw ITSM-hulp programma wijzigen in een Azure Log Analytics-werk ruimte.

Zie de [privacyverklaring van micro soft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)voor informatie over juridische voor waarden en het privacybeleid.

U kunt ITSMC gaan gebruiken door de volgende stappen uit te voeren:

1. [Stel uw ITSM-omgeving in om waarschuwingen van Azure te accepteren.](./itsmc-connections.md)
1. [Azure ITSM-oplossing configureren](./itsmc-definition.md#add-it-service-management-connector)
1. [Configureer de Azure ITSM-connector voor uw ITSM-omgeving.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configureer de actie groep om gebruik te maken van de ITSM-connector.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)