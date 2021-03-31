---
title: Uw back-ups controleren met backup Explorer
description: In dit artikel wordt beschreven hoe u met back-up-Verkenner realtime-bewaking van back-ups in kluizen, abonnementen, regio's en tenants uitvoert.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: a50b9ee05be48113221f2a12f968540bd3a00b3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824409"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Uw back-ups controleren met backup Explorer

Als organisaties een back-up maken van meer en meer machines naar de Cloud, wordt het steeds belang rijker om deze back-ups efficiënt te bewaken. De beste manier om te beginnen is door een centrale locatie te gebruiken voor het weer geven van operationele informatie over een groot goed.

Backup Explorer is een ingebouwde Azure Monitor werkmap waarmee Azure Backup klanten deze ene centrale locatie. Met back-up Verkenner kunt u operationele activiteiten bewaken voor het hele back-upbestand op Azure, voor het omspannen van tenants, locaties, abonnementen, resource groepen en kluizen. In ruime mate biedt backup Explorer de volgende mogelijkheden:

* **Op schaal**: krijg een geaggregeerde weer gave van de back-upitems, taken, waarschuwingen, beleids regels en resources die nog niet zijn geconfigureerd voor back-up in het hele erfgoed.
* **Analyse van inzoomen**: gedetailleerde informatie over elk van uw taken, waarschuwingen, beleids regels en back-upitems op één plek weer geven.
* **Interfaces waarvoor actie** kan worden uitgevoerd: nadat u een probleem hebt geïdentificeerd, kunt u dit oplossen door probleemloos naar het relevante back-upitem of de Azure-resource te gaan.

Deze mogelijkheden worden out-of-Box door systeem eigen integratie geboden met Azure resource Graph en Azure Monitor werkmappen.

> [!NOTE]
>
> * Backup Explorer is momenteel alleen beschikbaar voor Azure virtual machines-gegevens (Vm's).
> * Backup Explorer is bedoeld als operationeel dash board voor het weer geven van informatie over uw back-ups in de afgelopen 7 dagen (maximum).
> * Backup Explorer wordt momenteel niet ondersteund in nationale Clouds.
> * Op dit moment wordt de back-upverkenner-sjabloon niet ondersteund.
> * Het is niet raadzaam om aangepaste Automatiseringen te schrijven op Azure-resource grafiek gegevens.
> * Op dit moment kunt u met backup Explorer back-ups controleren over Maxi maal 1000 abonnementen (tussen tenants).

## <a name="get-started"></a>Aan de slag

U kunt back-upverkenner openen door naar een van uw Recovery Services-kluizen te gaan en de koppeling **back-up** te selecteren in het deel venster **overzicht** .

![Snelle koppeling van kluis](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Als u de koppeling selecteert, wordt back-upverkenner geopend, waarmee een geaggregeerde weer gave wordt geboden over alle kluizen en abonnementen waartoe u toegang hebt. Als u een Azure Lighthouse-account gebruikt, kunt u gegevens weer geven over alle tenants waartoe u toegang hebt. Zie de sectie ' cross-Tenant weergaven ' aan het einde van dit artikel voor meer informatie.

![Landings pagina van back-up Verkenner](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Use cases in back-up Verkenner

In back-upverkenner worden meerdere tabbladen weer gegeven, elk met gedetailleerde informatie over een specifieke back-upartefact (bijvoorbeeld een back-upitem, taak of beleid). Deze sectie bevat een kort overzicht van elk van de tabbladen. De Video's bieden voor beelden van gebruiks voorbeelden voor elk back-upartefact, samen met beschrijvingen van de beschik bare besturings elementen.

### <a name="the-summary-tab"></a>Het tabblad samen vatting

Het tabblad **samen vatting** bevat een kort overzicht van de algemene voor waarden van uw back-up. U kunt bijvoorbeeld het aantal items dat wordt beveiligd, bekijken, het aantal items waarvoor de beveiliging niet is ingeschakeld, of hoeveel taken in de afgelopen 24 uur zijn geslaagd.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Het tabblad Back-upitems

U kunt elk van uw back-upitems filteren en weer geven op basis van het abonnement, de kluis en andere kenmerken. Als u de naam van een back-upitem selecteert, kunt u het deel venster Azure voor dat item openen. U kunt bijvoorbeeld zien dat de laatste back-up is mislukt voor item *X* in de tabel. Als u *X* selecteert, kunt u het deel venster **back-up** van het item openen, waar u een back-upbewerking op aanvraag kunt activeren.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Het tabblad taken

Selecteer het tabblad **taken** om de details weer te geven van alle taken die in de afgelopen zeven dagen zijn geactiveerd. Hier kunt u filteren op *taak bewerking*, *taak status* en *fout code* (voor mislukte taken).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Het tabblad waarschuwingen

Selecteer het tabblad **waarschuwingen** om details weer te geven van alle waarschuwingen die in de afgelopen 7 dagen zijn gegenereerd voor uw kluizen. U kunt waarschuwingen filteren op type (*back-upfout* of *herstel fout*), huidige status (*actief* of *opgelost*) en Ernst (*kritiek*, *waarschuwing* of *informatie*). U kunt ook een koppeling selecteren om de virtuele Azure-machine te bezoeken en alle benodigde actie ondernemen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Het tabblad beleid

U kunt het tabblad **beleid** selecteren voor het weer geven van belang rijke informatie over alle back-upbeleiden die zijn gemaakt in uw back-up. U kunt het aantal items weer geven dat is gekoppeld aan elk beleid, samen met de Bewaar termijn en de back-upfrequentie die door het beleid zijn opgegeven.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Het tabblad niet ingeschakeld voor back-up

De back-up moet zijn ingeschakeld voor alle machines waarvoor beveiliging is vereist. Met backup Explorer kunnen back-upbeheerders snel bepalen welke computers in een organisatie nog niet zijn beveiligd met back-up. Als u deze informatie wilt weer geven, selecteert u het tabblad **back-up is niet ingeschakeld** .

In het deel venster **back-up is niet ingeschakeld** wordt een tabel weer gegeven met een lijst met niet-beveiligde machines. Uw organisatie kan verschillende tags toewijzen aan productie machines en test machines, of op machines met verschillende functies. Omdat voor elke klasse van machines een afzonderlijk back-upbeleid nodig is, kunt u met de filtering op labels specifieke informatie bekijken. Als u de naam van een computer selecteert, wordt u omgeleid naar het deel venster voor het configureren van de **back-up** van die computer, waarin u kunt kiezen om een toepasselijk back-upbeleid toe te passen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exporteren naar Excel

U kunt de inhoud van een tabel of diagram exporteren als een Excel-spread sheet. De inhoud wordt geëxporteerd als is, waarbij uw bestaande filters zijn toegepast. Als u extra tabel rijen wilt exporteren, kunt u het aantal rijen dat moet worden weer gegeven op de pagina verhogen door de vervolg keuzelijst **rijen per pagina** boven aan elk tabblad te gebruiken.

## <a name="pin-to-the-dashboard"></a>Vastmaken aan het dash board

U kunt het pictogram ' vastmaken ' boven aan elke tabel of grafiek selecteren om het vast te maken aan uw Azure Portal dash board. Als u deze gegevens vastmaakt, kunt u een aangepast dash board maken dat is aangepast om de informatie weer te geven die het belangrijkst is voor u.

## <a name="cross-tenant-views"></a>Cross-Tenant weergaven

Als u een Azure Lighthouse-gebruiker bent met gedelegeerde toegang tot abonnementen in meerdere Tenant omgevingen, kunt u het standaard abonnements filter gebruiken. U geeft de abonnementen weer waarvoor u gegevens wilt weer geven door in de rechter bovenhoek van het Azure Portal het pictogram filter te selecteren. Wanneer u deze functie gebruikt, wordt in back-upverkenner informatie geaggregeerd over alle kluizen in de geselecteerde abonnementen. Zie [Wat is Azure Lighthouse?](../lighthouse/overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van Azure Monitor voor het verkrijgen van inzichten in uw back-upgegevens](./backup-azure-monitoring-use-azuremonitor.md)
