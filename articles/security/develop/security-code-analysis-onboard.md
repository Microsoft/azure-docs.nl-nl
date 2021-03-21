---
title: Hand leiding voor analyse van micro soft-beveiligings code
description: Meer informatie over het voorbereiden en installeren van de uitbrei ding voor de analyse van beveiligings codes van micro soft. Zie vereisten en aanvullende bronnen weer geven.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 30d6061e15b8eb17c1450500d9e8c141a83a3e08
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553915"
---
# <a name="onboarding-and-installing"></a>Onboarding en installeren

Vereisten om aan de slag te gaan met de analyse van micro soft-beveiligings code:

- Een in aanmerking komend Microsoft Unified Support aanbieding, zoals beschreven in de volgende sectie.
- Een Azure DevOps-organisatie.
- Machtiging voor het installeren van extensies voor de Azure DevOps-organisatie.
- Bron code die kan worden gesynchroniseerd met een door de Cloud gehoste Azure DevOps-pijp lijn.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>De uitbrei ding van de analyse van micro soft-beveiligings code onboarden

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Wilt u de analyse-uitbrei ding voor micro soft-beveiligings code aanschaffen?

Als u een van de volgende ondersteunings aanbiedingen hebt, neemt u contact op met uw technische account beheerder om bestaande uren te kopen of te wisselen om toegang te krijgen tot de uitbrei ding:

- Geavanceerde laag voor ondersteuning van Unified support
- Prestatie niveau Unified support
- Premier Support voor ontwikkel aars
- Premier Support voor partners
- Premier Support voor bedrijven

Als u niet beschikt over een van de hierboven genoemde ondersteunings overeenkomsten, kunt u de uitbrei ding kopen bij een van onze partners.

**Volgende stappen:**

Als u voldoet aan de bovenstaande kwalificaties, neemt u contact op met een partner uit de onderstaande lijst om de uitbrei ding voor de analyse van beveiligings codes van micro soft te kopen Neem anders contact op met de [ondersteuning van micro soft security code Analysis](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Partner**

- Zones: contact gegevens: cloudsupport@zones.com
- Wortell – contact gegevens: info@wortell.nl
- Logischis-contact gegevens: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Een partner worden

Het micro soft security code Analysis-Team is bezig met het voorbereiden van partners met een Premier Support voor partner overeenkomst. Met partners kunnen Azure DevOps-klanten veiliger ontwikkelen door de uitbrei ding te verkopen aan klanten die deze willen kopen, maar ze hebben geen Enter prise-ondersteunings overeenkomst met micro soft. Geïnteresseerde partners kunnen [hier](http://www.microsoftpartnersupport.com/msrd/opin)registreren.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>De uitbrei ding voor de analyse van micro soft-beveiligings code installeren

1. Nadat de extensie is gedeeld met uw Azure DevOps-organisatie, gaat u naar de pagina Azure DevOps-organisatie. Een voor beeld van een URL voor een dergelijke pagina is `https://dev.azure.com/contoso` .
1. Selecteer het pictogram winkel Bag in de rechter bovenhoek naast uw naam en selecteer vervolgens **extensies beheren**.
1. Selecteer **gedeeld**.
1. Selecteer de uitbrei ding voor de analyse van micro soft-beveiligings code, selecteer **installeren**.
1. Kies in de vervolg keuzelijst de Azure DevOps-organisatie waarop u de extensie wilt installeren.
1. Selecteer **Installeren**. Nadat de installatie is voltooid, kunt u beginnen met het gebruik van de extensie.

>[!NOTE]
> Ook als u geen toegang hebt voor het installeren van de uitbrei ding, gaat u door met de installatie stappen. U kunt tijdens het installatie proces toegang aanvragen bij de organisatie beheerder van Azure DevOps.

Nadat u de uitbrei ding hebt geïnstalleerd, zijn de taken voor het ontwikkelen van beveiligde ontwikkel aars zichtbaar en beschikbaar voor toevoegen aan uw Azure-pijp lijnen.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Specifieke build-taken toevoegen aan uw Azure DevOps-pijp lijn

1. Open uw team project uit uw Azure DevOps-organisatie.
1. Selecteer **pijp lijnen**  >  **builds**.
1. Selecteer de pijp lijn waaraan u de extensie taken wilt toevoegen:
   - Nieuwe pijp lijn: Selecteer **Nieuw** en volg de stappen die worden beschreven om een nieuwe pijp lijn te maken.
   - Pijp lijn bewerken: Selecteer een bestaande pijp lijn en selecteer **bewerken** om te beginnen met het bewerken van de pijp lijn.
1. Selecteer **+** en ga naar het deel venster **taken toevoegen** .
1. Zoek in de lijst of via het zoekvak de build-taak die u wilt toevoegen. Selecteer **Toevoegen**.
1. Geef de para meters op die nodig zijn voor de taak.
1. Een nieuwe build in de wachtrij plaatsen.
   >[!NOTE]
   >Paden voor bestanden en mappen zijn relatief ten opzichte van de hoofdmap van de bron opslagplaats. Als u de uitvoer bestanden en-mappen opgeeft als para meters, worden deze vervangen door de algemene locatie die we hebben gedefinieerd in de build-agent.

> [!TIP]
>
> - Als u na uw Build een analyse wilt uitvoeren, plaatst u de taken voor het analyseren van micro soft-beveiligings code na de stap build bouwen artefacten van uw build. Op die manier kan uw build de resultaten volt ooien en plaatsen voordat statische analyse Programma's worden uitgevoerd.
> - Selecteer altijd **door gaan bij fout** voor het maken van beveiligde ontwikkel taken. Zelfs als één hulp programma mislukt, kunnen de andere functies worden uitgevoerd. Er zijn geen onderlinge afhankelijkheden tussen hulpprogram ma's.
> - Micro soft security code Analysis-taken kunnen alleen worden uitgevoerd als het uitvoeren van een hulp programma is mislukt. Maar ze slagen zelfs als een hulp programma problemen in de code identificeert. Met behulp van de taak voor het maken van een post-analyse kunt u instellen dat uw build mislukt wanneer een hulp programma problemen in de code identificeert.
> - Sommige Azure DevOps-build-taken worden niet ondersteund wanneer ze worden uitgevoerd via een release pijplijn. Meer specifiek: Azure DevOps biedt geen ondersteuning voor taken die artefacten publiceren vanuit een release pijplijn.
> - Zie [Azure DevOps build Varia bles](/azure/devops/pipelines/build/variables?tabs=batch)(Engelstalig) voor een lijst met vooraf gedefinieerde variabelen in azure DevOps team build die u als para meters kunt opgeven.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het configureren van de build-taken raadpleegt u onze [configuratie](security-code-analysis-customize.md) handleiding of de [yaml-configuratie handleiding](yaml-configuration.md).

Bekijk onze [pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding en de hulpprogram ma's die worden aangeboden.