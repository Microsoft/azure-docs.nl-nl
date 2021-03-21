---
title: Visual Studio gebruiken op een virtuele Azure-machine
description: Visual Studio gebruiken op een virtuele machine van Azure.
author: cathysull
manager: cathys
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: cathys
keywords: Visual Studio
ms.openlocfilehash: a6acf27743f17941e14adc3f6deea903f4f5ca87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562075"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-installatie kopieën op Azure
Het gebruik van Visual Studio in een vooraf geconfigureerde Azure virtual machine (VM) is een snelle en eenvoudige manier om van niets naar een ontwikkel omgeving te gaan. Systeem kopieën met verschillende Visual Studio-configuraties zijn beschikbaar op de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Bent u nog niet bekend met Azure? [Maak een gratis Azure-account](https://azure.microsoft.com/free).

> [!NOTE]
> Niet alle abonnementen komen in aanmerking voor het implementeren van Windows 10-installatie kopieën. Zie [Windows-client in azure gebruiken voor ontwikkel-en test scenario's](./client-images.md) voor meer informatie.

## <a name="what-configurations-and-versions-are-available"></a>Welke configuraties en versies zijn er beschikbaar?
Installatie kopieën voor de meest recente primaire versies, Visual Studio 2019, Visual Studio 2017 en Visual Studio 2015, vindt u op de Azure Marketplace.  Voor elke uitgebrachte primaire versie ziet u de oorspronkelijke versie van ' release to Web ' (RTW) en de meest recente bijgewerkte versies.  Elk van deze versies biedt Visual Studio Enter prise en de edities van de Visual Studio-community.  Deze installatie kopieën worden minstens elke maand bijgewerkt met de nieuwste Visual Studio-en Windows-updates.  Hoewel de namen van de afbeeldingen hetzelfde blijven, bevat de beschrijving van de installatie kopie de geïnstalleerde product versie en de datum van de installatie kopie.

| Release versie                                                                                                                                                | Edities              | Productversie   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: nieuwste (versie 16,8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enter prise, Community | Versie 16.8.0    |
| Visual Studio 2019: RTW                         | Enterprise | Versie 16.0.20    |
| Visual Studio 2017: nieuwste (versie 15,9)           | Enter prise, Community | Versie 15.9.29   |
| Visual Studio 2017: RTW                             | Enter prise, Community | Versie 15.0.28  |
| Visual Studio 2015: nieuwste (update 3)               | Enter prise, Community | Versie 14.0.25431.01 |

> [!NOTE]
> In overeenstemming met het micro soft-onderhouds beleid is de oorspronkelijk vrijgegeven (RTW) versie van Visual Studio 2015 verlopen voor onderhoud. Visual Studio 2015 update 3 is de enige resterende versie die wordt aangeboden voor de product lijn van Visual Studio 2015.

Zie voor meer informatie het [Visual Studio-onderhouds beleid](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Welke functies zijn geïnstalleerd?
Elke installatie kopie bevat de aanbevolen functieset voor die Visual Studio Edition. Over het algemeen geldt de volgende installatie:

* Alle beschik bare werk belastingen, inclusief de aanbevolen optionele onderdelen van elke werk belasting
* .NET 4.6.2-en .NET 4,7-Sdk's, doel pakketten en Ontwikkelhulpprogramma's
* Visueel element F #
* GitHub-extensie voor Visual Studio
* LINQ to SQL-Hulpprogram Ma's

De opdracht regel die wordt gebruikt om Visual Studio te installeren tijdens het maken van de installatie kopieën is als volgt:

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

Als de installatie kopieën geen Visual Studio-functie bevatten die u nodig hebt, kunt u feedback geven via het hulp programma feedback in de rechter bovenhoek van de pagina.

## <a name="what-size-vm-should-i-choose"></a>Welke VM moet ik kiezen?
Azure biedt een breed scala aan grootten voor virtuele machines. Omdat Visual Studio een krachtige, multi thread-toepassing is, wilt u een VM-grootte hebben die ten minste twee processors en 7 GB aan geheugen bevat. De volgende VM-grootten worden aanbevolen voor de Visual Studio-installatie kopieën:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Zie [grootten voor virtuele Windows-machines in azure](../sizes.md)voor meer informatie over de meest recente computer grootten.

Met Azure kunt u uw oorspronkelijke keuze herverdelen door de grootte van de virtuele machine te wijzigen. U kunt een nieuwe VM inrichten met een meer geschikte grootte of het formaat van uw bestaande virtuele machine aanpassen aan andere onderliggende hardware. Zie [het formaat van een Windows-VM wijzigen](./resize-vm.md)voor meer informatie.

## <a name="after-the-vm-is-running-whats-next"></a>Wat gebeurt er als de VM actief is?
Visual Studio volgt het model ' uw eigen licentie meenemen ' in Azure. Net als bij een installatie op eigen hardware is een van de eerste stappen een licentie voor uw Visual Studio-installatie. Als u Visual Studio wilt ontgrendelen, gaat u als volgt te:
- Meld u aan met een Microsoft-account dat is gekoppeld aan een Visual Studio-abonnement 
- Ontgrendel Visual Studio met de product code die bij uw eerste aankoop werd geleverd

Zie voor meer informatie [Aanmelden bij Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) en [hoe u Visual Studio kunt ontgrendelen](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hoe kan ik de ontwikkelings-VM opslaan voor toekomstig gebruik of voor een team?

Het spectrum van ontwikkel omgevingen is enorm en er zijn echte kosten verbonden aan het bouwen van de complexere omgevingen. Ongeacht de configuratie van uw omgeving kunt u uw geconfigureerde virtuele machine opslaan of vastleggen als basis installatie kopie voor toekomstig gebruik of voor andere leden van uw team. Wanneer u vervolgens een nieuwe virtuele machine opstart, moet u deze van de basis installatie kopie inrichten in plaats van de Azure Marketplace-installatie kopie.

Een snelle samen vatting: gebruik het hulp programma voor systeem voorbereiding (Sysprep) en sluit de actieve VM af en leg vervolgens *(afbeelding 1)* de virtuele machine vast als een installatie kopie via de gebruikers interface in de Azure Portal. In azure wordt het `.vhd` bestand met de installatie kopie opgeslagen in het opslag account van uw keuze. De nieuwe installatie kopie wordt vervolgens weer gegeven als een afbeeldings resource in de lijst met resources van uw abonnement.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Afbeelding 1) Leg een installatie kopie vast via de Azure Portal-gebruikers interface.*</center>

Zie [een beheerde installatie kopie maken van een gegeneraliseerde vm in azure](./capture-image-resource.md)voor meer informatie.

> [!IMPORTANT]
> Vergeet niet om Sysprep te gebruiken om de virtuele machine voor te bereiden. Als u deze stap mist, kan Azure geen virtuele machine inrichten van de installatie kopie.

> [!NOTE]
> Er worden nog steeds kosten in rekening gebracht voor de opslag van de installatie kopieën, maar deze incrementele kosten kunnen niet significant zijn vergeleken met de overhead kosten om de virtuele machine opnieuw te bouwen voor elk teamlid dat er een nodig heeft. Het kost bijvoorbeeld een paar dollar om een installatie kopie van 127 GB te maken en op te slaan voor een maand die door uw hele team kan worden gebruikt. Deze kosten zijn echter niet significant in vergelijking met uren die elke werk nemer investeren om een correct geconfigureerde dev box te maken en te valideren voor hun eigen gebruik.

Daarnaast hebben uw ontwikkel taken of technologieën mogelijk meer schaal, zoals de variëteiten van ontwikkel configuraties en configuraties met meerdere computers. U kunt Azure DevTest Labs gebruiken om _recepten_ te maken die de bouw van uw ' gouden installatie kopie ' automatiseren. U kunt ook DevTest Labs gebruiken om beleid te beheren voor de actieve Vm's van uw team. Het [gebruik van Azure DevTest Labs voor ontwikkel aars](../../devtest-labs/devtest-lab-developer-lab.md) is de beste bron voor meer informatie over DevTest Labs.

## <a name="next-steps"></a>Volgende stappen
Nu u bekend bent met de vooraf geconfigureerde Visual Studio-installatie kopieën, is de volgende stap het maken van een nieuwe VM:

* [Een virtuele machine maken via de Azure Portal](quick-create-portal.md)
* [Overzicht van Windows Virtual Machines](overview.md)