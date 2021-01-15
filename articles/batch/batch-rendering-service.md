---
title: Overzicht van rendering
description: Inleiding tot het gebruik van Azure voor rendering en een overzicht van de mogelijkheden van Azure Batch-Rendering
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232111"
---
# <a name="rendering-using-azure"></a>Weergeven met Azure

Rendering is het proces van het maken van 3D-modellen en het converteren ervan naar 2D-afbeeldingen. 3D-scène bestanden worden in toepassingen geschreven, zoals auto Desk 3ds Max, Autodesk Maya en blender.  Het renderen van toepassingen zoals Autodesk Maya, Autodesk Arnold, chaos groep V-Ray en overvloei cycli produceren 2D-installatie kopieën.  Soms worden afzonderlijke installatie kopieën uit de scène bestanden gemaakt. Het is echter gebruikelijk om meerdere installatie kopieën te model leren en weer te geven en deze vervolgens te combi neren in een animatie.

De werk belasting voor rendering wordt intensief gebruikt voor speciale effecten (VFX) in de media en entertainment industrie. Rendering wordt ook gebruikt in veel andere branches, zoals reclame, detailhandel, olie- en gasindustrie, en productie.

Het proces voor rendering is reken intensief; Er kunnen veel frames/afbeeldingen worden gemaakt en elke afbeelding kan veel uur duren om weer te geven.  Rendering is daarom een perfecte batch verwerkings werkbelasting die Azure kan gebruiken voor het uitvoeren van veel Renders parallel en het gebruik van een breed scala aan hardware, inclusief Gpu's.

## <a name="why-use-azure-for-rendering"></a>Waarom Azure voor Rendering gebruiken?

Rendering is om verschillende redenen een werk belasting die perfect geschikt is voor Azure:

* Rendering-taken kunnen worden gesplitst in veel onderdelen die parallel kunnen worden uitgevoerd met meerdere Vm's:
  * Animaties bestaan uit een groot aantal frames en elk frame kan parallel worden weer gegeven.  Hoe meer Vm's er beschikbaar zijn voor het verwerken van elk frame, hoe sneller alle frames en de animatie kunnen worden geproduceerd.
  * Met sommige beeldrenderings software kunnen afzonderlijke frames worden opgedeeld in meerdere delen, zoals tegels of segmenten.  Elk stuk kan afzonderlijk worden gerenderd en vervolgens in de uiteindelijke afbeelding worden gecombineerd wanneer alle onderdelen zijn voltooid.  Hoe meer Vm's er beschikbaar zijn, hoe sneller een frame kan worden weer gegeven.
* Voor het renderen van projecten kunnen zeer grote schalen worden vereist:
  * Afzonderlijke frames kunnen ingewikkeld zijn en veel uur duren om weer te geven, zelfs op een geavanceerde hardware. animaties kunnen bestaan uit honderd duizenden frames.  Een enorme hoeveelheid reken kracht is vereist voor het renderen van animaties van hoge kwaliteit in een redelijke hoeveelheid tijd.  In sommige gevallen zijn er meer dan 100.000 kern geheugens gebruikt om duizenden frames parallel weer te geven.
* Rendering van projecten is gebaseerd op een project en vereisen verschillende hoeveel heden reken kracht:
  * Wijs reken-en opslag capaciteit toe, indien nodig, schaal deze naar boven of beneden op basis van de belasting tijdens een project en verwijder deze wanneer een project is voltooid.
  * U betaalt voor de capaciteit wanneer deze is toegewezen, maar niet voor deze betaling wanneer er geen belasting is, zoals tussen projecten.
  * Keuken voor bursts vanwege onverwachte wijzigingen; schaal hoger als er onverwachte wijzigingen in een project aanwezig zijn en die wijzigingen moeten worden verwerkt op een strak schema.
* Kies uit een breed scala aan hardware op basis van de toepassing, workload en tijds bestek:
  * Er is een brede selectie van hardware beschikbaar in azure die kan worden toegewezen en beheerd met batch.
  * Afhankelijk van het project is de vereiste mogelijk voor de beste prijs-prestatie verhouding of de beste algehele prestaties.  Verschillende scènes en/of rendering-toepassingen hebben andere geheugen vereisten.  Sommige rendering-toepassingen kunnen gebruikmaken van Gpu's voor de beste prestaties of bepaalde functies. 
* De kosten voor de [virtuele machines](https://azure.microsoft.com/pricing/spot/) met lage prioriteit of op een plek verlagen:
  * Virtuele machines met lage prioriteit en spots zijn beschikbaar voor een grote korting vergeleken met standaard-Vm's en zijn geschikt voor sommige taak typen.
  
## <a name="existing-on-premises-rendering-environment"></a>Bestaande on-premises rendering-omgeving

Het meest voorkomende geval is dat er een bestaande on-premises render-farm wordt beheerd door een beeldrenderings toepassing, zoals PipelineFX Qube, Royal rendering, Thinkbox deadline of een aangepaste toepassing.  De vereiste is om de on-premises weer gave-Farm capaciteit uit te breiden met behulp van virtuele Azure-machines.

Azure-infra structuur en-services worden gebruikt voor het maken van een hybride omgeving waarin Azure wordt gebruikt om de on-premises capaciteit aan te vullen. Bijvoorbeeld:

* Gebruik een [Virtual Network](../virtual-network/virtual-networks-overview.md) om de Azure-resources te plaatsen op hetzelfde netwerk als de on-premises render-farm.
* Gebruik [avere vFXT voor Azure](../avere-vfxt/avere-vfxt-overview.md) of [Azure HPC cache](../hpc-cache/hpc-cache-overview.md) om bron bestanden in azure op te slaan in de cache om het gebruik en de latentie van de band breedte te beperken en de prestaties te optimaliseren.
* Zorg ervoor dat de bestaande licentie server zich in het virtuele netwerk bevindt en schaf de extra licenties aan die vereist zijn voor de extra op Azure gebaseerde capaciteit.

## <a name="no-existing-render-farm"></a>Geen bestaande render-farm

Client werkstations kunnen rendering uitvoeren, maar de weer gave-belasting neemt toe en het duurt te lang om alleen de capaciteit van werk stations te gebruiken.

Er zijn twee belang rijke opties beschikbaar:

* Implementeer een on-premises render Manager, zoals Royal rendering, en configureer een hybride omgeving voor het gebruik van Azure wanneer verdere capaciteit of prestaties vereist zijn. Een beeldrenderings Manager is speciaal afgestemd op werk belastingen en bevat invoeg toepassingen voor de populaire client toepassingen, waardoor het mogelijk is om weergave taken eenvoudig in te dienen.

* Een aangepaste oplossing met behulp van Azure Batch voor het toewijzen en beheren van de reken capaciteit en het leveren van de taak planning voor het uitvoeren van de weergave taken.

## <a name="next-steps"></a>Volgende stappen

 Meer informatie over het [gebruik van Azure-infra structuur en-services voor het uitbreiden van een bestaande on-premises render-farm](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Meer informatie over [Azure batch-weergave mogelijkheden](batch-rendering-functionality.md).
