---
title: Office 365-logboeken verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Office 365-logboek connector om informatie over voortdurende gebruikers-en beheer activiteiten in Exchange, teams en share point, waaronder OneDrive, te gebruiken.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05848eb2761284669e659b3875e96acdfa71f90f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632179"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Office 365-logboeken verbinden met Azure Sentinel

De [Office 365](/office/) -logboek connector brengt Azure-Sentinel-informatie over op lopende gebruikers-en beheer activiteiten in **Exchange** en **share point** (inclusief **OneDrive**), en nu ook in **teams** . Deze informatie omvat Details over acties zoals het downloaden van bestanden, het verzenden van toegangs aanvragen, wijzigingen in groeps gebeurtenissen, Postvak bewerkingen, teams gebeurtenissen (zoals chat-, team-, lid-en kanaal gebeurtenissen), evenals de details van de gebruiker die de acties heeft uitgevoerd. Door Office 365-logboeken te koppelen aan Azure Sentinel kunt u deze gegevens in uw werkmappen weer geven en analyseren, query's uitvoeren om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek proces te verbeteren, zodat u meer inzicht hebt in uw Office 365-beveiliging.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet een globale beheerder of beveiligings beheerder zijn op uw Tenant.

- Uw Office 365-implementatie moet zich op dezelfde Tenant bevinden als uw Azure Sentinel-werk ruimte.

> [!IMPORTANT]
> - Om ervoor te zorgen dat de connector toegang kan krijgen tot gegevens via de API voor Office 365-beheer activiteit, moet **Unified audit logging** zijn ingeschakeld voor uw Office 365-implementatie. Afhankelijk van het type Office 365/Microsoft 365 licentie dat u hebt, kan het niet standaard worden ingeschakeld. Raadpleeg het [Office 365 Security and Compliance Center](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) voor het controleren van de status van Unified audit logging volgens uw licentie type.
> - U kunt de huidige status van de gecombineerde audit logboeken van Office 365 ook hand matig inschakelen, uitschakelen en controleren. Zie voor instructies het [Office 365-controle logboek zoeken in-of uitschakelen](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Zie voor meer informatie de [Office 365 Management Activity API-referentie](/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Zoals hierboven vermeld, en zoals u ziet op de pagina connector onder **gegevens typen**, ondersteunt de Azure Sentinel Office 365-connector momenteel alleen de opname van audit logboeken vanuit micro soft Exchange en share point (inclusief OneDrive), **en nu ook van teams**. Er zijn echter een aantal externe oplossingen als u geïnteresseerd bent in [andere Office-gegevens](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) in azure Sentinel te brengen. 

## <a name="enable-the-office-365-log-connector"></a>De Office 365-logboek connector inschakelen

### <a name="instructions-tab"></a>Tabblad instructies

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer in de galerie **Data connectors** de optie **Office 365** en selecteer vervolgens **connector pagina openen** in het voorbeeld venster.

1. Schakel onder de sectie met de naam **configuratie** de selectie vakjes in van de Office 365-activiteiten logboeken die u wilt verbinden met Azure Sentinel en klik op **wijzigingen Toep assen**. 

   > [!NOTE]
   > Als u eerder meerdere tenants met Azure Sentinel hebt verbonden met een oudere versie van de Office 365-connector die dit ondersteunt, kunt u de logboeken bekijken en wijzigen die u van elke Tenant verzamelt. U kunt geen extra tenants toevoegen, maar u moet eerder toegevoegde tenants verwijderen.

### <a name="next-steps-tab"></a>Tabblad volgende stappen

- Zie de aanbevolen werkmappen, query voorbeelden en analyse regel sjablonen die zijn gebundeld in de **Office 365** -logboek connector, om inzicht te krijgen in de logboek gegevens van share point, OneDrive, Exchange en teams.

- Als u hand matig een query wilt uitvoeren voor Office 365 logboek gegevens in **Logboeken**, typt u `OfficeActivity` in de eerste regel van het query venster.
   - Als u de query voor een specifiek logboek type wilt filteren, typt u `| where OfficeWorkload == "<logtype>"` in de tweede regel van de query, waarbij *\<logtype\>* `SharePoint` of, `OneDrive` `Exchange` , of `MicrosoftTeams` .

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Office 365 kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.