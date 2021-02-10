---
title: Overzicht van IT Service Management-connector
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: d22bb05ad6db3630e9b0242e098fd81f65e34b05
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007309"
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
> Met ingang van 1-okt-2020 Cher well-en Provance ITSM-integratie met Azure-waarschuwing is niet langer beschikbaar voor nieuwe klanten. Nieuwe ITSM-verbindingen worden niet ondersteund.
> Bestaande ITSM-verbindingen worden ondersteund.

Met ITSMC kunt u het volgende doen:

-  Maak werk items in uw ITSM-hulp programma, op basis van uw Azure-waarschuwingen (metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en Log Analytics waarschuwingen).
-  U kunt eventueel uw incident synchroniseren en gegevens van uw ITSM-hulp programma wijzigen in een Azure Log Analytics-werk ruimte.

Zie de [privacyverklaring van micro soft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)voor informatie over juridische voor waarden en het privacybeleid.

U kunt ITSMC gaan gebruiken door de volgende stappen uit te voeren:

1. [Stel uw ITSM-omgeving in om waarschuwingen van Azure te accepteren.](./itsmc-connections.md)
1. [Azure ITSM-oplossing configureren](./itsmc-definition.md#add-it-service-management-connector)
1. [Configureer de Azure ITSM-connector voor uw ITSM-omgeving.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configureer de actie groep om gebruik te maken van de ITSM-connector.](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)
