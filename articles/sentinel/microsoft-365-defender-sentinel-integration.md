---
title: Integratie van Microsoft 365 Defender met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe Microsoft 365 Defender-integratie met Azure Sentinel u de mogelijkheid biedt om Azure Sentinel als uw universele incidenten wachtrij te gebruiken terwijl M365D's-sterkten worden behouden om te helpen bij het onderzoeken van M365-beveiligings incidenten, en hoe u in de geavanceerde jacht-gegevens van Defender-componenten kunt opnemen in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745922"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integratie van Microsoft 365 Defender met Azure Sentinel

> [!IMPORTANT]
> De Microsoft 365 Defender-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** was voorheen bekend als **micro soft Threat Protection** of **MTP**.
>
> **Micro soft Defender voor eind punt** was voorheen bekend als **micro soft Defender Advanced Threat Protection** of **MDATP**.
>
> U ziet mogelijk dat de oude namen gedurende een bepaalde periode nog steeds worden gebruikt.

## <a name="incident-integration"></a>Integratie van incidenten

Met de integratie van Azure Sentinel [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kunt u alle M365D-incidenten streamen naar Azure Sentinel en ze gesynchroniseerd houden tussen beide portals. Incidenten van M365D (voorheen bekend als micro soft Threat Protection of MTP) bevatten alle gerelateerde waarschuwingen, entiteiten en relevante informatie, zodat u over voldoende context beschikt voor het uitvoeren van sorteren en voorlopig onderzoek in azure Sentinel. Eenmaal in Sentinel blijven incidenten bidirectionele gesynchroniseerd met M365D, zodat u kunt profiteren van de voor delen van beide portals in uw incident onderzoek.

Deze integratie geeft Microsoft 365 beveiligings incidenten de zicht baarheid van binnen Azure Sentinel, als onderdeel van de primaire wachtrij van de incidenten in de hele organisatie, zodat u M365-incidenten kunt zien in combi natie met die van alle andere Cloud-en on-premises systemen. Tegelijkertijd kunt u profiteren van de unieke kracht en mogelijkheden van M365D voor uitgebreid onderzoek en een M365-specifieke ervaring in het M365-ecosysteem... M365 Defender verrijkt en groepeert waarschuwingen van meerdere M365-producten, waardoor de incidenten wachtrij van de SOC minder groot wordt en de tijd wordt verkort die moet worden opgelost. De onderdeel Services die deel uitmaken van de M365 Defender-stack zijn:

- **Micro soft Defender voor eind punt** (MDE, voorheen MDATP)
- **Micro soft Defender for Identity** (MDI, voorheen AATP)
- **Micro soft Defender voor Office 365** (MDO, voorheen O365ATP)
- **Microsoft Cloud app Security** (MCAS)

Naast het verzamelen van waarschuwingen van deze onderdelen, genereert M365 Defender zelf waarschuwingen. Er worden incidenten van al deze waarschuwingen gemaakt en deze worden naar Azure Sentinel verzonden.

### <a name="common-use-cases-and-scenarios"></a>Veelvoorkomende use cases en scenario's

- Met één klik op verbinding maken met M365 Defender-incidenten, inclusief alle waarschuwingen en entiteiten van M365 Defender-onderdelen, in azure Sentinel.

- Bidirectionele synchronisatie tussen Sentinel-en M365D-incidenten bij status, eigenaar en afsluit reden.

- Maak gebruik van M365 Defender-waarschuwings groeperingen en-verrijkings mogelijkheden in azure Sentinel, waardoor u minder tijd kunt oplossen.

- Een diep gaande koppeling in de context tussen een onderliggend Azure-incident en het parallelle M365 Defender-incident, om onderzoek over beide portals te vergemakkelijken.

### <a name="connecting-to-microsoft-365-defender"></a>Verbinding maken met Microsoft 365 Defender

Zodra u de Microsoft 365 Defender Data Connector voor het [verzamelen van incidenten en waarschuwingen](connect-microsoft-365-defender.md)hebt ingeschakeld, worden M365D-incidenten weer gegeven in de wachtrij met meldingen van Azure-Sentinel-incidenten, met **Microsoft 365 Defender** in het veld **product naam** , kort nadat ze zijn gegenereerd in M365 Defender.
- Het kan tot 10 minuten duren vanaf het moment dat een incident in M365 Defender wordt gegenereerd tot de tijd die in de Azure-Sentinel wordt weer gegeven.

- Incidenten worden opgenomen en zonder extra kosten gesynchroniseerd.

Zodra de M365-integratie is verbonden, worden alle connectors voor waarschuwings signalen voor onderdelen (MDE, MDI, MDO, MCAS) automatisch op de achtergrond aangesloten als deze nog niet aanwezig zijn. Als er onderdeel licenties zijn aangeschaft nadat M365 Defender was verbonden, zullen de waarschuwingen en incidenten van het nieuwe product nog steeds stromen naar Azure Sentinel zonder extra configuratie of kosten.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender-incidenten en regels voor het maken van micro soft-incidenten

- Incidenten die door M365 Defender worden gegenereerd op basis van waarschuwingen die afkomstig zijn van M365-beveiligings producten, worden gemaakt met aangepaste M365 Logic.

- Regels voor het maken van micro soft-incidenten in azure Sentinel maken ook incidenten van dezelfde waarschuwingen, met (een andere) aangepaste Azure-Sentinel logica.

- Als beide mechanismen samen worden gebruikt, wordt dit volledig ondersteund. deze configuratie kan worden gebruikt om de overgang naar de nieuwe logica voor het maken van M365 Defender-incidenten te vergemakkelijken. Dit maakt echter **duplicaat incidenten** voor dezelfde waarschuwingen.

- Om te voor komen dat er dubbele incidenten worden gemaakt voor dezelfde waarschuwingen, raden we aan dat klanten alle regels voor het **maken van micro soft-incidenten** voor M365-producten (MDE-, MDI-en MDO-Zie MCAS hieronder) uitschakelen bij het verbinden van M365 Defender. U kunt dit doen door het selectie vakje op de pagina connector te markeren. Als u dit doet, worden filters die zijn toegepast door de regels voor het maken van incidenten, niet toegepast op M365 Defender-incident integratie.

- Voor Microsoft Cloud App Security-waarschuwingen (MCAS) zijn niet alle waarschuwings typen momenteel onboarded voor M365 Defender. Om ervoor te zorgen dat u nog steeds incidenten krijgt voor alle MCAS-waarschuwingen, moet u regels voor het **maken van micro soft-incidenten** behouden of maken voor de waarschuwings typen die *niet zijn geboardd* naar M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Werken met M365 Defender-incidenten in azure Sentinel en bidirectionele Sync

M365 Defender-incidenten worden weer gegeven in de wachtrij met Sentinel-incidenten van Azure met de product naam **Microsoft 365 Defender** en met vergelijk bare Details en functionaliteit voor andere Sentinel-incidenten. Elk incident bevat een koppeling terug naar het parallelle incident in de M365 Defender-Portal.

Wanneer het incident zich ontwikkelt in M365 Defender en er meer waarschuwingen of entiteiten aan worden toegevoegd, wordt het Azure Sentinel-incident dienovereenkomstig bijgewerkt.

Wijzigingen in de status, sluitings reden of toewijzing van een M365-incident, in M365D of Azure Sentinel, worden ook dienovereenkomstig bijgewerkt in de wachtrij met incidenten van de andere. De synchronisatie vindt plaats in beide portals onmiddellijk nadat de wijziging van het incident is toegepast, zonder vertraging. Het kan zijn dat er een vernieuwing is vereist om de meest recente wijzigingen te zien.

In M365 Defender kunnen alle waarschuwingen van het ene incident naar het andere worden overgedragen, waardoor de incidenten worden samengevoegd. Als dit gebeurt, worden de wijzigingen in de Azure-Sentinel-incidenten weer gegeven. Eén incident bevat alle waarschuwingen van de oorspronkelijke incidenten en het andere incident wordt automatisch gesloten, met het label ' omgeleid '.

> [!NOTE]
> Incidenten in azure Sentinel kunnen Maxi maal 150 waarschuwingen bevatten. M365D-incidenten kunnen meer dan dit hebben. Als een M365D-incident met meer dan 150 waarschuwingen is gesynchroniseerd met Azure Sentinel, wordt het verklikker incident weer gegeven als ' 150 + ' waarschuwingen en wordt een koppeling naar het parallelle incident in M365D geboden, waar u de volledige set waarschuwingen ziet.

## <a name="advanced-hunting-event-collection"></a>Geavanceerde jacht-gebeurtenissen verzamelen

Met de Microsoft 365 Defender connector kunt u ook **geavanceerde jacht** -gebeurtenissen streamen: een type onbewerkte gebeurtenis gegevens, van Microsoft 365 Defender en de Component Services naar Azure Sentinel. U kunt momenteel [micro soft Defender voor eind punten (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [Advanced-jacht](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) gebeurtenissen verzamelen en deze rechtstreeks in de door het doel ontwikkelde tabellen in uw Azure Sentinel-werk ruimte streamen. Deze tabellen zijn gebaseerd op hetzelfde schema dat wordt gebruikt in de Microsoft 365 Defender-Portal, zodat u volledige toegang hebt tot de volledige set geavanceerde jacht-gebeurtenissen, zodat u het volgende kunt doen:

- Kopieer eenvoudig uw bestaande micro soft Defender for Endpoint-query's voor geavanceerde jacht naar Azure Sentinel.

- Gebruik de onbewerkte gebeurtenis Logboeken om extra inzichten te bieden voor uw waarschuwingen, te jacht en te onderzoeken en deze gebeurtenissen te correleren met andere gegevens bronnen in azure Sentinel.

- Sla de logboeken op met een verbeterde Bewaar periode van meer dan micro soft Defender voor eind punt of de standaard retentie van Microsoft 365 Defender van 30 dagen. U kunt dit doen door de Bewaar periode van uw werk ruimte te configureren of door de Bewaar periode per tabel in Log Analytics te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Microsoft 365 Defender samen met Azure Sentinel kunt gebruiken met behulp van de Microsoft 365 Defender connector.

- Instructies ophalen voor [het inschakelen van de Microsoft 365 Defender connector](connect-microsoft-365-defender.md).
- [Aangepaste waarschuwingen](tutorial-detect-threats-custom.md) maken en [incidenten onderzoeken](tutorial-investigate-cases.md).
