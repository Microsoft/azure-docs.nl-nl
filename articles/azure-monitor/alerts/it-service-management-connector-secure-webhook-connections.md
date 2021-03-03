---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor
description: In dit artikel wordt beschreven hoe u uw ITSM-producten of-Services verbindt met beveiligde export in Azure Monitor om ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: b1295904f25d38c97578fa6fd6ede4ecd50c0456
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728974"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van beveiligde export

In dit artikel wordt beschreven hoe u de verbinding tussen het ITSM-product of de service van uw IT-Service beheer (met behulp van beveiligde export) kunt configureren.

Secure export is een bijgewerkte versie van [IT Service Management-connector (ITSMC)](./itsmc-overview.md). Met beide versies kunt u werk items maken in een ITSM-hulp programma wanneer Azure Monitor waarschuwingen verzendt. De functionaliteit omvat metrische, logboek-en activiteiten logboek waarschuwingen.

ITSMC maakt gebruik van gebruikers naam en wacht woord. Beveiligd exporteren heeft sterkere verificatie omdat deze gebruikmaakt van Azure Active Directory (Azure AD). Azure AD is de identiteits- en toegangsbeheerservice van Microsoft op basis van de cloud. Het helpt gebruikers zich aan te melden en toegang te krijgen tot interne of externe resources. Met Azure AD met ITSM kunt u Azure-waarschuwingen identificeren (via de Azure AD-toepassings-ID) die naar het externe systeem zijn verzonden.

> [!NOTE]
> De mogelijkheid om Azure te verbinden met ITSM-hulpprogram ma's met behulp van beveiligde export is in preview.

## <a name="secure-export-architecture"></a>Beveiligde export architectuur

De Secure export-architectuur introduceert de volgende nieuwe mogelijkheden:

* **Nieuwe actie groep**: waarschuwingen worden verzonden naar het ITSM-hulp programma via de actie groep van de beveiligde webhook, in plaats van de ITSM actie groep die ITSMC gebruikt.
* **Azure AD-verificatie**: de verificatie vindt plaats via Azure AD in plaats van de referenties van de gebruikers naam en het wacht woord.

## <a name="secure-export-data-flow"></a>Gegevens stroom voor beveiligde export

De stappen voor de beveiligde export gegevens stroom zijn:

1. Azure Monitor verzendt een waarschuwing die is geconfigureerd voor het gebruik van beveiligde export.
2. De nettolading van de waarschuwing wordt door een beveiligde webhook-actie verzonden naar het ITSM-hulp programma.
3. De ITSM-toepassing controleert met Azure AD als de waarschuwing is gemachtigd om het ITSM-hulp programma in te voeren.
4. Als de waarschuwing is geautoriseerd, wordt de toepassing:
   
   1. Hiermee maakt u een werk item (bijvoorbeeld een incident) in het ITSM-hulp programma.
   2. Koppelt de ID van het configuratie-item (CI) aan de klant beheer database (CMDB).

![Diagram dat laat zien hoe het ITSM-hulp programma communiceert met Azure A D, Azure-waarschuwingen en een actie groep.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Voor delen van veilig exporteren

De belangrijkste voor delen van de integratie zijn:

* **Betere verificatie**: Azure AD biedt een veiligere verificatie zonder de time-outs die zich vaak voordoen in ITSMC.
* **Waarschuwingen die zijn opgelost in het ITSM-hulp programma**: metrische waarschuwingen implementeren ' fireed ' en ' opgeloste ' statussen. Wanneer aan de voor waarde wordt voldaan, wordt de status van de waarschuwing geactiveerd. Als niet meer aan de voor waarde wordt voldaan, is de waarschuwings status opgelost. In ITSMC kunnen waarschuwingen niet automatisch worden opgelost. Met beveiligde export stromen de omgezette status naar het ITSM-hulp programma en worden deze automatisch bijgewerkt.
* **[Gebruikelijk waarschuwings schema](./alerts-common-schema.md)**: in ITSMC verschilt het schema van de nettolading van de waarschuwing op basis van het waarschuwings type. Bij beveiligde export is er een gemeen schappelijk schema voor alle waarschuwings typen. Dit algemene schema bevat de CI voor alle waarschuwings typen. Alle waarschuwings typen kunnen hun CI binden met de CMDB.

## <a name="next-steps"></a>Volgende stappen

* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-overview.md)