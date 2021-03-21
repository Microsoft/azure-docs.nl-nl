---
title: Beste praktijken
description: Aanbevolen procedures voor het verbeteren van de resultaten
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: e287d8305b3fd85fc992417e1563b1e58e6f8424
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462997"
---
# <a name="best-practices"></a>Aanbevolen procedures

We raden u aan enkele van deze stappen uit te voeren om de beste resultaten te krijgen.

## <a name="ingestion"></a>Gegevensopname

- Controleer de afmetingen van uw fysieke objecten. Azure object-ankers werken het beste voor objecten waarvan de kleinste dimensie zich in het bereik van de aanbevolen 1M-10 miljoen bevindt.
- Inspecteer uw 3D-model in software zoals [**MeshLab**](https://www.meshlab.net/) voor de volgende details.
  - Zorg ervoor dat het 3D-model een drie hoekige mesh heeft en dat de drie hoeken op het buitenste Opper vlak naar buiten. Dat wil zeggen dat de hoek punten moeten worden georiënteerd om de normalen de juiste regel te laten volgen in hun richting.
  - Zorg ervoor dat het 3D-model is opgegeven met de juiste schaal eenheden ten opzichte van de fysieke objecten. De eenheden moeten een van de volgende zijn: ***centimeters, decimeters, meters, inches, kilo meters, millimeters en yards***.
  - Bevestig de nominale zwaarte richting die overeenkomt met de werkelijke wereld wijde verticale stand van het object. Als de neerwaartse verticaal/zwaarte kracht van het object-Y is, gebruikt u ***(0,-1, 0)** _ of _*_ (0, 0,-1) _ * * voor-Z en ook voor een andere richting.
  - Zorg ervoor dat het 3D-model is gecodeerd met een van de ondersteunde indelingen: `.glb` ,, `.gltf` `.ply` , `.fbx` , `.obj` .
- Onze model conversie service kan veel tijd in beslag nemen om een groot, hoog LOD-model (detail niveau) te verwerken. Voor de effectiviteit kunt u uw 3D-model voors verwerken om de interne gezichten te verwijderen.

## <a name="detection"></a>Detectie

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- De meegeleverde runtime-SDK vereist een door de gebruiker verschaft Zoek regio voor het zoeken en detecteren van de fysieke object (en). De zoek regio kan een selectie kader, een bol, een weer gave-frustum of een combi natie hiervan zijn. Om een onjuiste detectie te voor komen, is het raadzaam om een zoek regio in te stellen die groot genoeg is om het object te kunnen behandelen. Wanneer u de meegeleverde voor beeld-apps gebruikt, kunt u aan één zijde van het object ongeveer 2 meters van het dichtstbijzijnde Opper vlak behalen en de app starten.
- Voordat u de Azure object-ankers-app op een HoloLens 2-apparaat start, verwijdert u de hologrammen in de buurt van uw werk plek via de hoofd instellingen van uw apparaten via ***instellingen->systeem >hologrammen***

  Met deze stap zorgt u ervoor dat als een nieuw object, zoals een auto, zich in dezelfde ruimte bevindt als de andere, of omdat het object is verplaatst van de doel ruimte, eventuele oude en irrelevante hologrammen niet blijven bestaan en een verwarrende visualisatie maken voor het object dat momenteel wordt weer gegeven.
- Nadat u de hologrammen hebt verwijderd en voordat u de app hebt gestart, moet u het object, zoals een auto, scannen door te kijken naar het object terwijl u het apparaat over ongeveer 1-2 meters en traag om het object één of twee keer kunt door lopen.

  Met deze stap zorgt u ervoor dat eventuele residu schattingen die in uw ruimte worden gemaakt door eerdere objecten en scans worden vernieuwd met de Opper vlakken van het huidige doel object waarmee u wilt werken. Anders is het mogelijk dat er dubbele Ghost-Opper vlakken worden weer geven die leiden tot onjuiste uitlijning van uw 3D-model en de bijbehorende hologrammen. Bij het vooraf scannen van het object wordt ook de latentie van de detectie van Azure-objecten aanzienlijk verminderd, van 30 seconden tot 5 seconden.
- Voor donkere en uiterst reflecterende objecten moet u het object mogelijk in een dichter bereik scannen en ook door uw hoofd omhoog en omlaag en naar beneden en naar links en rechts te verplaatsen zodat het apparaat Opper vlakken van meerdere hoeken en meerdere afstanden kan weer geven.
- Wanneer er een onjuiste object detectie wordt weer geven, zoals de stand die wordt gespiegeld of de verkeerde fout, zoals een gekanteld model, moet u de ruimtelijke toewijzing visualiseren. Vaak worden de onjuiste resultaten veroorzaakt door slechte of onvolledige oppervlakte reconstructie. U kunt de hologrammen verwijderen, het object scannen en object detectie op de app opnieuw uitvoeren.
- De opgegeven runtime-SDK biedt een aantal para meters waarmee gebruikers de detectie kunnen afstemmen, zoals wordt getoond in onze voor beeld-apps. De standaard parameters werken voor de meeste objecten goed. Als u merkt dat u deze voor specifieke objecten moet aanpassen, zijn hier enkele aanbevelingen:
  - Gebruik een lagere drempel waarde voor dekking van Opper vlakken als het fysieke object groot, donker of glanzend is.
  - Een wijziging in een kleine schaal toestaan (bijvoorbeeld 0,1) voor een groot object zoals een auto.
  - Laat enige afwijking in graden tussen de lokale verticale richting van het object en de ernst wanneer het object zich op een helling bevindt.
