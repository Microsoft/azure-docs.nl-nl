---
title: Microsoft Threat Modeling Tool versie 9/12/2018
titleSuffix: Azure
description: Lees de opmerkingen bij de release voor de Microsoft Threat Modeling Tool uitgebracht op 9/12/2018. De opmerkingen bevatten functie wijzigingen en oplossingen voor fouten.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 7b0afdd90f2a1413c7f8364fc4518c4d1116454a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913582"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA release 7.1.50911.2-9/12/2018

Met trots kondigen we de Microsoft Threat Modeling Tool nu beschikbaar om te downloaden als een ondersteunde, algemeen beschik bare versie (GA). Deze release bevat belang rijke privacy-en beveiligings updates, evenals oplossingen voor problemen, functie-updates en stabiliteits verbeteringen. Bestaande gebruikers van de preview-versie van 2017 wordt gevraagd om bij te werken naar de nieuwste versie via de ClickOnce-technologie bij het openen van de client. Voor nieuwe gebruikers van het hulp programma kunt u [de client downloaden](https://aka.ms/threatmodelingtool).

In deze release bieden we ondersteuning voor de preview-versie van 2017 en raden we aan alle gebruikers van de preview-update uit te voeren voor de GA-release. Op of na 15 2018 oktober zullen we de mini maal vereiste ClickOnce-versie voor de Threat Modeling Tool instellen en moeten alle preview-clients worden bijgewerkt.

De Microsoft Threat Modeling Tool 2016, die beschikbaar is via het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=49168), blijft ondersteund tot oktober 1 2019 voor kritieke beveiligings oplossingen.

## <a name="feature-changes"></a>Functie wijzigingen

### <a name="azure-stencil-updates"></a>Updates van het Azure-stencil

Extra Azure-stencils en de bijbehorende dreigingen en oplossingen zijn toegevoegd aan de ingestelde stencilset met deze release. Er zijn belang rijke wijzigingen aangebracht in de focus gebieden ' Azure-app Services ', ' aanbiedingen van Azure data base ' en ' Azure Storage '.

![Updates van het Azure-stencil](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>De functie voor integratie van OneDrive is verwijderd

De functies ' opslaan in OneDrive ', ' openen vanuit OneDrive ' en ' een koppeling delen ' van de preview zijn verwijderd. Gebruikers van OneDrive worden aanbevolen de [onedrive voor Windows](https://onedrive.live.com/about/en-us/download/) -clients van micro soft te gebruiken voor toegang tot hun bestanden die zijn opgeslagen in OneDrive via de standaard bestanden opslaan en openen in het dialoog venster.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Belang rijke vaste fouten die door klanten worden gemeld

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>In TMT preview loopt het hulp programma vast wanneer de standaard sjabloon wordt gebruikt

- Wanneer een algemeen stencil (bijvoorbeeld ' algemene gegevens stroom ') wordt toegevoegd aan het teken oppervlak en bedreigingen genereert, kan het hulp programma vastlopen. Dit probleem is opgelost.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>In TMT preview, wanneer ik een rapport opsla of de bedreigingen Kopieer, zijn de risico niveaus onjuist

- Als een gebruiker het risico niveau van specifieke dreigingen wijzigt en vervolgens een rapport opslaat of de Risico's kopieert, kan het risico niveau worden teruggezet op hoog. Dit probleem is opgelost.

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Gebruikers van schermen met een hoge resolutie kunnen kleine tekst in de bedreigings eigenschappen ondervinden

#### <a name="issue"></a>Probleem

Als de gebruiker in de analyse weergave van het hulp programma een scherm met een hoge resolutie heeft dat standaard wordt ingesteld om de Lees baarheid in Windows te verg Roten, wordt de sectie mogelijke problemen met een bedreiging weer gegeven met kleine tekst.

![Bekend probleem met schermen met een hoge resolutie](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Tijdelijke oplossing

De gebruiker kan op de tekst van de oplossing klikken en het standaard Windows zoom-besturings element (crtl-muis wiel) gebruiken om de vergroting van die sectie te verg Roten.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Bestanden in de sectie onlangs geopende modellen van het hoofd venster kunnen niet worden geopend

#### <a name="issue"></a>Probleem

De functie openen vanuit OneDrive van de preview-versie is verwijderd. Gebruikers met ' onlangs geopende modellen ' die zijn opgeslagen in OneDrive, ontvangen de volgende fout.

![Scherm afbeeldingen toont een fout: fout bij het openen van de bestands object verwijzing is niet ingesteld op een exemplaar van een object.](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Tijdelijke oplossing

Gebruikers van OneDrive worden aanbevolen de [onedrive voor Windows](https://onedrive.live.com/about/en-us/download/) -clients van micro soft te gebruiken voor toegang tot hun bestanden die zijn opgeslagen in OneDrive via het dialoog venster standaard en een model openen.

![In het dialoog venster een model openen ziet u dat OneDrive is geselecteerd.](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Mijn organisatie gebruikt de 2016-versie van het hulp programma, kan ik de Azure stencil set gebruiken?

Ja, u kunt! De [Azure stencil set is beschikbaar op github](https://github.com/Microsoft/threat-modeling-templates/)en kan worden geladen in de 2016-versie van het hulp programma. Als u een nieuw model wilt maken met de Azure-stencilset, gebruikt u het dialoog venster sjabloon voor nieuwe modellen in het hoofd menu scherm. TMT 2016 kan de koppelingen die worden gevonden in de velden ' mogelijke oplossingen ' van de Azure-stencilset niet weer geven, waardoor u mogelijk koppelingen ziet die worden weer gegeven als HTML-tags.

![Updates van het Azure-stencil in 2016-client](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - Micro soft Windows 10
- .NET-versie vereist
  - .NET-3.5.2
- Aanvullende vereisten
  - Er is een Internet verbinding vereist voor het ontvangen van updates voor het hulp programma en voor sjablonen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van het hulp programma](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).
