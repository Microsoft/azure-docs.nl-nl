---
title: Een Visual Studio op een virtuele Azure-machine gebruiken
description: Gebruik Visual Studio op een virtuele Azure-machine.
author: andysterland
manager: andster
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: andster
keywords: visualstudio
ms.openlocfilehash: f26bec64012e0b0909b7df5422c57ff2cb1c347e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478553"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio in Azure maken
Het Visual Studio in een vooraf geconfigureerde virtuele Azure-machine (VM) is een snelle en eenvoudige manier om van niets naar een up-and-running ontwikkelomgeving te gaan. Systeeminstallatiebestanden met Visual Studio configuraties zijn beschikbaar in [de Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Bent u nog niet bekend met Azure? [Maak een gratis Azure-account.](https://azure.microsoft.com/free)

> [!NOTE]
> Niet alle abonnementen komen in aanmerking voor het implementeren van Windows 10-afbeeldingen. Zie Use [Windows client in Azure for dev/test scenarios (Windows-client in Azure gebruiken voor dev/test-scenario's) voor meer informatie](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Welke configuraties en versies zijn beschikbaar?
Afbeeldingen voor de meest recente belangrijke versies, Visual Studio 2019, Visual Studio 2017 en Visual Studio 2015, vindt u in de Azure Marketplace.  Voor elke uitgebrachte hoofdversie ziet u de oorspronkelijke RTW-versie (released to web) en de meest recente bijgewerkte versies.  Elk van deze versies biedt de Visual Studio Enterprise- en Visual Studio Community-edities.  Deze afbeeldingen worden ten minste elke maand bijgewerkt met de meest recente Visual Studio en Windows-updates.  Hoewel de namen van de installatie afbeeldingen hetzelfde blijven, bevat de beschrijving van elke installatielijn de geïnstalleerde productversie en de datum 'vanaf' van de installatie afbeelding.

| Releaseversie                                                                                                                                                | Edities              | Productversie   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Latest (versie 16.8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, Community | Versie 16.8.0    |
| Visual Studio 2019: RTW                         | Enterprise | Versie 16.0.20    |
| Visual Studio 2017: Meest recente versie (versie 15.9)           | Enterprise, Community | Versie 15.9.29   |
| Visual Studio 2017: RTW                             | Enterprise, Community | Versie 15.0.28  |
| Visual Studio 2015: Meest recente versie (Update 3)               | Enterprise, Community | Versie 14.0.25431.01 |

> [!NOTE]
> In overeenstemming met het Microsoft-onderhoudsbeleid is de oorspronkelijk uitgebrachte (RTW)-versie van Visual Studio 2015 verlopen voor onderhoud. Visual Studio 2015 Update 3 is de enige resterende versie die wordt aangeboden voor de Visual Studio 2015-productlijn.

Zie voor meer informatie het [Visual Studio servicebeleid.](https://www.visualstudio.com/productinfo/vs-servicing-vs)

## <a name="what-features-are-installed"></a>Welke functies zijn geïnstalleerd?
Elke afbeelding bevat de aanbevolen functieset voor die Visual Studio-editie. Over het algemeen omvat de installatie:

* Alle beschikbare workloads, inclusief de aanbevolen optionele onderdelen van elke workload. Meer informatie over de werkbelastingen, onderdelen en SDK's die Visual Studio vindt u in de [Visual Studio documentatie](https://docs.microsoft.com/visualstudio/install/workload-and-component-ids)
* .NET 4.6.2 en .NET 4.7 SDK's, doelpakketten en Ontwikkelhulpprogramma's
* Visual F #
* GitHub-extensie voor Visual Studio
* LINQ to SQL Tools

De opdrachtregel die wordt gebruikt om Visual Studio installatie te installeren bij het bouwen van de installatie afbeeldingen is als volgt:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Als de afbeeldingen geen functie Visual Studio bevatten die u nodig hebt, geeft u feedback via het feedbackhulpprogramma in de rechterbovenhoek van de pagina.

## <a name="what-size-vm-should-i-choose"></a>Welke grootte moet ik kiezen voor de VM?
Azure biedt een volledig scala aan grootten voor virtuele machines. Omdat Visual Studio een krachtige toepassing met meerdere threads is, wilt u een VM-grootte met ten minste twee processors en 7 GB geheugen. We raden de volgende VM-grootten aan voor de Visual Studio-afbeeldingen:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Zie Grootten voor virtuele Windows-machines in Azure voor meer informatie over de meest [recente machinegrootten.](../sizes.md)

Met Azure kunt u uw eerste keuze opnieuw in balans brengen door het formaat van de VM te herstellen. U kunt een nieuwe VM inrichten met een geschiktere grootte of de grootte van uw bestaande VM naar andere onderliggende hardware. Zie Het besturingssysteem van [een Windows-VM voor meer informatie.](./resize-vm.md)

## <a name="after-the-vm-is-running-whats-next"></a>Wat gebeurt er nadat de VM wordt uitgevoerd?
Visual Studio volgt het 'Bring Your Own License'-model in Azure. Net als bij een installatie op eigen hardware, is een van de eerste stappen het licentieverlenen van Visual Studio installatie. U kunt Visual Studio volgende Visual Studio ontgrendelen:
- Aanmelden met een Microsoft-account die is gekoppeld aan een Visual Studio-abonnement 
- Ontgrendel Visual Studio met de productcode die bij uw eerste aankoop is gekocht

Zie Aanmelden bij Visual Studio [en](/visualstudio/ide/signing-in-to-visual-studio) Het ontgrendelen van Visual Studio [voor meer Visual Studio.](/visualstudio/ide/how-to-unlock-visual-studio)

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hoe kan ik de ontwikkel-VM opslaan voor toekomstig gebruik of voor teamgebruik?

Het spectrum van ontwikkelomgevingen is enorm en er zijn werkelijke kosten verbonden aan het bouwen van de complexere omgevingen. Ongeacht de configuratie van uw omgeving kunt u uw geconfigureerde VM opslaan of vastleggen als basisafbeelding voor toekomstig gebruik of voor andere leden van uw team. Wanneer u vervolgens een nieuwe VM opstart, kunt u deze inrichten vanuit de basisafbeelding in plaats van de Azure Marketplace in.

Een beknopt overzicht: gebruik het hulpprogramma voor systeemvoorbereiding (Sysprep) en sluit de virtuele machine af en leg vervolgens *(afbeelding 1)* de VM vast als een installatieprogramma via de gebruikersinterface in de Azure Portal. Azure slaat het `.vhd` bestand met de afbeelding op in het opslagaccount van uw keuze. De nieuwe afbeelding wordt vervolgens weergegeven als een afbeeldingsresource in de lijst met resources van uw abonnement.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(afbeelding 1) Leg een afbeelding vast via de Azure Portal ui.*</center>

Zie Create [a managed image of a generalized VM in Azure (Een beheerde afbeelding van een ge generaliseerde VM maken in Azure) voor meer informatie.](./capture-image-resource.md)

> [!IMPORTANT]
> Vergeet niet om Sysprep te gebruiken om de VM voor te bereiden. Als u deze stap mist, kan Azure geen VM inrichten vanuit de -afbeelding.

> [!NOTE]
> Er worden nog steeds kosten in rekening brengen voor de opslag van de afbeeldingen, maar die incrementele kosten kunnen worden vergeleken met de overheadkosten voor het opnieuw opbouwen van de VM voor elk teamlid dat er een nodig heeft. Het kost bijvoorbeeld een paar dollar om een afbeelding van 127 GB te maken en op te slaan voor een maand die opnieuw kan worden gebruikt door uw hele team. Deze kosten zijn echter vergelijkbaar met het aantal uren dat elke werknemer investeert om een correct geconfigureerde dev-box te bouwen en te valideren voor individueel gebruik.

Daarnaast hebben uw ontwikkelingstaken of -technologieën mogelijk meer schaal nodig, zoals varianten van ontwikkelingsconfiguraties en meerdere computerconfiguraties. U kunt Azure DevTest Labs om recepten te _maken die_ de bouw van uw 'gouden afbeelding' automatiseren. U kunt DevTest Labs ook gebruiken om beleid te beheren voor de VM's die door uw team worden uitgevoerd. [Het Azure DevTest Labs voor ontwikkelaars](../../devtest-labs/devtest-lab-developer-lab.md) is de beste bron voor meer informatie over DevTest Labs.

## <a name="next-steps"></a>Volgende stappen
Nu u weet wat de vooraf geconfigureerde Visual Studio zijn, is de volgende stap het maken van een nieuwe VM:

* [Een VM maken via de Azure Portal](quick-create-portal.md)
* [Windows Virtual Machines overzicht](overview.md)
