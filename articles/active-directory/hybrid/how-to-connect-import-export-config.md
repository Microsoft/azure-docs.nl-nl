---
title: Azure AD Connect configuratie-instellingen importeren en exporteren
description: In dit artikel worden veelgestelde vragen over het inrichten van Clouds beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681952"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Azure AD Connect configuratie-instellingen importeren en exporteren 

Azure Active Directory (Azure AD) Connect-implementaties variëren van één installatie van de forest Express-modus tot complexe implementaties die worden gesynchroniseerd tussen meerdere forests met behulp van aangepaste synchronisatie regels. Vanwege het grote aantal configuratie opties en-mechanismen is het belang rijk om te begrijpen welke instellingen van kracht zijn en snel een server met een identieke configuratie kunnen implementeren. Deze functie introduceert de mogelijkheid om de configuratie van een bepaalde synchronisatie server te catalogiseren en de instellingen te importeren in een nieuwe implementatie. Verschillende moment opnamen van synchronisatie-instellingen kunnen worden vergeleken om eenvoudig de verschillen tussen twee servers te visualiseren of dezelfde server gedurende een bepaalde periode.

Telkens wanneer de configuratie wordt gewijzigd vanuit de wizard Azure AD Connect, wordt er automatisch een nieuw bestand met gestempelde JSON-instellingen geëxporteerd naar **%ProgramData%\AADConnect**. De naam van het instellingen bestand is van het formulier **toegepast-SynchronizationPolicy-*. JSON**, waarbij het laatste deel van de bestands naam een tijds tempel is.

> [!IMPORTANT]
> Alleen wijzigingen die door Azure AD Connect zijn aangebracht, worden automatisch geëxporteerd. Wijzigingen die zijn aangebracht met behulp van Power shell, de Synchronization Service Manager, of de editor voor synchronisatie regels, moeten naar behoefte worden geëxporteerd om een bijgewerkte kopie te onderhouden. Exporteren op aanvraag kan ook worden gebruikt om een kopie van de instellingen op een veilige locatie te plaatsen voor herstel na nood gevallen.

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect-instellingen exporteren 

Als u een samen vatting van uw configuratie-instellingen wilt weer geven, opent u het hulp programma Azure AD Connect en selecteert u de extra taak met de naam **weer geven of de huidige configuratie exporteren**. Er wordt een snelle samen vatting van uw instellingen weer gegeven, samen met de mogelijkheid om de volledige configuratie van uw server te exporteren.

Standaard worden de instellingen geëxporteerd naar **%ProgramData%\AADConnect**. U kunt er ook voor kiezen om de instellingen op een beveiligde locatie op te slaan om te zorgen voor Beschik baarheid als er sprake is van een ramp. Instellingen worden geëxporteerd met behulp van de JSON-bestands indeling en mogen niet hand matig worden gemaakt of bewerkt om logische consistentie te garanderen. Het importeren van een door een hand gemaakt of bewerkt bestand wordt niet ondersteund en kan leiden tot onverwachte resultaten.

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect-instellingen importeren

Eerder geëxporteerde instellingen importeren:
 
1. Installeer **Azure AD Connect** op een nieuwe server.
1. Selecteer de optie **aanpassen** na de **welkomst** pagina.
1. Selecteer **synchronisatie-instellingen importeren**. Blader naar het eerder geëxporteerde bestand met JSON-instellingen.
1. Selecteer **Installeren**.

   ![Scherm opname van het venster vereiste onderdelen installeren](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Instellingen op deze pagina negeren zoals het gebruik van SQL Server in plaats van LocalDB of het gebruik van een bestaand service account in plaats van een standaard LEVERANCIERSPECIFIEKE waarde. Deze instellingen worden niet geïmporteerd uit het bestand met configuratie-instellingen. Ze zijn er voor informatie en vergelijkings doeleinden.

### <a name="import-installation-experience"></a>Installatie-ervaring importeren 

De ervaring bij het importeren van de installatie wordt opzettelijk eenvoudig gehouden met minimale invoer van de gebruiker, zodat de reproduceer baarheid van een bestaande server eenvoudig kan worden verbeterd.

Dit zijn de enige wijzigingen die tijdens de installatie-ervaring kunnen worden aangebracht. Alle andere wijzigingen kunnen na de installatie worden aangebracht vanuit de wizard Azure AD Connect:
- **Azure Active Directory referenties**: de account naam voor de Azure Global-beheerder die wordt gebruikt voor het configureren van de oorspronkelijke server, wordt standaard voorgesteld. Het *moet*   worden gewijzigd als u informatie wilt synchroniseren met een nieuwe map.
- **Aanmelding door gebruiker**: de aanmeldings opties die zijn geconfigureerd voor de oorspronkelijke server, worden standaard geselecteerd en vragen om referenties of andere informatie die nodig is tijdens de configuratie. In zeldzame gevallen is het mogelijk dat er een server met verschillende opties moet worden ingesteld om te voor komen dat het gedrag van de actieve server wordt gewijzigd. Anders selecteert u **volgende** om dezelfde instellingen te gebruiken.
- **On-premises Directory referenties**: voor elke on-premises Directory die is opgenomen in uw synchronisatie-instellingen, moet u referenties opgeven voor het maken van een synchronisatie account of een vooraf gemaakte, aangepaste synchronisatie account opgeven. Deze procedure is identiek aan de ervaring met schone installatie met de uitzonde ring dat u geen directory's kunt toevoegen of verwijderen.
- **Configuratie opties**: net als bij een schone installatie kunt u de initiële instellingen configureren om te bepalen of u automatische synchronisatie wilt starten of de faserings modus wilt inschakelen. Het belangrijkste verschil is dat de Faserings modus opzettelijk standaard is ingeschakeld, zodat de configuratie-en synchronisatie resultaten kunnen worden vergeleken voordat actief de resultaten naar Azure worden geëxporteerd.

![Scherm opname van het scherm verbinding maken met uw adres lijsten](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Er kan slechts één synchronisatie server zich in de primaire rol bevindt en de configuratie wijzigingen voor het actief naar Azure exporteert. Alle andere servers moeten in de Faserings modus worden geplaatst.

## <a name="migrate-settings-from-an-existing-server"></a>Instellingen van een bestaande server migreren 

Als een bestaande server instellingen beheer niet ondersteunt, kunt u ervoor kiezen om de server in-place te upgraden of de instellingen te migreren voor gebruik op een nieuwe staging-server.

Voor de migratie moet een Power shell-script worden uitgevoerd waarmee de bestaande instellingen worden geëxtraheerd voor gebruik in een nieuwe installatie.Gebruik deze methode om de instellingen van uw bestaande server te catalogiseren en deze vervolgens toe te passen op een nieuw geïnstalleerde staging-server.Als u de instellingen voor de oorspronkelijke server vergelijkt met een nieuw gemaakte server, worden de wijzigingen tussen de servers snel gevisualiseerd.Volg altijd het certificerings proces van uw organisatie om ervoor te zorgen dat er geen aanvullende configuratie is vereist.

### <a name="migration-process"></a>Migratieproces 
De instellingen migreren:

1. Start **AzureADConnect.msi** op de nieuwe staging-server en stop op de **welkomst** pagina van Azure AD Connect.

1. Kopieer **MigrateSettings.ps1** van de map Microsoft Azure AD Connect\Tools naar een locatie op de bestaande server. Een voor beeld is C:\setup, waarbij Setup een map is die is gemaakt op de bestaande server.

   ![Scherm opname van Azure AD Connect directory's.](media/how-to-connect-import-export-config/migrate1.png)

1. Voer het script uit zoals hier wordt weer gegeven en sla de volledige server configuratie directory op. Kopieer deze map naar de nieuwe staging-server. U moet de volledige **geëxporteerde-ServerConfiguration-*-** map naar de nieuwe server kopiëren.

   ![Scherm opname van het script in Windows Power shell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Scherm opname van het kopiëren van de geëxporteerde-ServerConfiguration-*-map.](media/how-to-connect-import-export-config/migrate3.png)

1. Start **Azure AD Connect** door te dubbel klikken op het pictogram op het bureau blad. Ga akkoord met de licentie voorwaarden voor micro soft-software en selecteer op de volgende pagina **aanpassen**.
1. Schakel het selectie vakje **synchronisatie-instellingen importeren** in. Selecteer **Bladeren** om door de gekopieerde-over geëxporteerde-ServerConfiguration-*-map te bladeren. Selecteer de MigratedPolicy.jsin om de gemigreerde instellingen te importeren.

   ![Scherm afbeelding met de optie synchronisatie-instellingen importeren.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Verificatie na de installatie 

Het vergelijken van het oorspronkelijk geïmporteerde instellingen bestand met het geëxporteerde-instellingen bestand van de zojuist geïmplementeerde server is een essentiële stap bij het goed keuren van eventuele verschillen tussen de beoogde implementaties. Met uw favoriete side-by-side-toepassing voor tekst vergelijking krijgt u een snelle visualisatie die snel de gewenste of onbedoelde wijzigingen markeert.

Hoewel veel eerder hand matige configuratie stappen nu zijn verwijderd, moet u nog steeds het certificerings proces van uw organisatie volgen om ervoor te zorgen dat er geen aanvullende configuratie is vereist. Deze configuratie kan optreden als u geavanceerde instellingen gebruikt, die momenteel niet worden vastgelegd in deze versie van instellingen beheer.

Hier volgen enkele bekende beperkingen:
- **Synchronisatie regels**: de prioriteit voor een aangepaste regel moet in het gereserveerde bereik van 0 tot 99 liggen om conflicten met de standaard regels van micro soft te voor komen. Als u een aangepaste regel buiten het gereserveerde bereik plaatst, kan dit ertoe leiden dat uw aangepaste regel wordt verplaatst als standaard regels worden toegevoegd aan de configuratie. Er treedt een soortgelijk probleem op als uw configuratie gewijzigde standaard regels bevat. Het wijzigen van een standaard regel wordt afgeraden en het is waarschijnlijk dat de plaatsing van de regel onjuist is.
- **Write-back van apparaat**: deze instellingen worden gecatalogiseerd. Ze worden momenteel niet toegepast tijdens de configuratie. Als write-back van het apparaat is ingeschakeld voor de oorspronkelijke server, moet u de functie hand matig configureren op de zojuist geïmplementeerde server.
- **Gesynchroniseerde object typen**: Hoewel het mogelijk is om de lijst met gesynchroniseerde object typen (zoals gebruikers, contact personen en groepen) te beperken met behulp van de Synchronization Service Manager, wordt deze functie momenteel niet ondersteund via synchronisatie-instellingen. Nadat u de installatie hebt voltooid, moet u de geavanceerde configuratie hand matig opnieuw Toep assen.
- **Aangepaste uitvoerings profielen**: Hoewel het mogelijk is om de standaard reeks uitvoerings profielen te wijzigen met behulp van de Synchronization Service Manager, wordt deze functie momenteel niet ondersteund via synchronisatie-instellingen. Nadat u de installatie hebt voltooid, moet u de geavanceerde configuratie hand matig opnieuw Toep assen.
- **De inrichtings hiërarchie configureren**: deze geavanceerde functie van de Synchronization Service Manager wordt niet ondersteund via synchronisatie-instellingen. Het moet hand matig opnieuw worden geconfigureerd nadat u de eerste implementatie hebt voltooid.
- **Active Directory Federation Services (AD FS) en PingFederate-verificatie**: de aanmeldings methoden die zijn gekoppeld aan deze verificatie functies, worden automatisch vooraf geselecteerd. U moet interactief alle andere vereiste configuratie parameters opgeven.
- **Een uitgeschakelde aangepaste synchronisatie regel wordt geïmporteerd als ingeschakeld**: een uitgeschakelde aangepaste synchronisatie regel wordt geïmporteerd als ingeschakeld. Zorg ervoor dat u dit ook op de nieuwe server uitschakelt.

 ## <a name="next-steps"></a>Volgende stappen

- [Hardware en vereisten](how-to-connect-install-prerequisites.md) 
- [Snelle instellingen](how-to-connect-install-express.md)
- [Aangepaste instellingen](how-to-connect-install-custom.md)
- [Azure AD Connect Health-agents installeren](how-to-connect-health-agent-install.md) 
