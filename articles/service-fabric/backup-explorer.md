---
title: Lokaal back-ups van betrouw bare verzamelingen lezen en bijwerken
description: Gebruik back-upverkenner in azure Service Fabric om een back-up van lokale betrouw bare verzamelingen te lezen en bij te werken.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86034881"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Een betrouw bare back-up van verzamelingen lezen en bijwerken met behulp van back-upverkenner

Azure Service Fabric backup Explorer helpt bij de gegevens correctie als gegevens beschadigd zijn in Service Fabric reliable-verzamelingen. De huidige status van de gegevens kan beschadigd raken door een bug die in een toepassing is geïntroduceerd of door onjuiste vermeldingen die zijn aangebracht in een live cluster.

Met behulp van back-upverkenner kunt u de volgende taken uitvoeren:
-   Query's uitvoeren op de meta gegevens voor de verzameling.
-   Bekijk de huidige status en de bijbehorende vermeldingen in de verzameling van de back-up die wordt geladen.
-   De trans acties weer geven die zijn uitgevoerd sinds het laatste controle punt.
-   Werk de verzameling bij door vermeldingen in de verzameling toe te voegen, bij te werken of te verwijderen.
-   Maak een nieuwe back-up met behulp van de bijgewerkte status.

> [!NOTE]
> Service Fabric backup Explorer ondersteunt momenteel alleen het weer geven en bewerken van betrouw bare verzamelingen in de back-up.
>

## <a name="access-the-backup"></a>Toegang tot de back-up

Service Fabric backup Explorer kan worden gebruikt op een van de volgende manieren om betrouw bare verzamelingen in de back-up te bekijken of bij te werken:
-   **Binair**: gebruik een NuGet-pakket om betrouw bare verzamelingen weer te geven en te wijzigen.
-   **Http/rest**: gebruik een op http gebaseerde rest-server om betrouw bare verzamelingen weer te geven en te wijzigen.
-   **bkpctl**: gebruik de service Fabric backup Explorer-opdracht regel interface (CLI) om een back-up van betrouw bare verzamelingen weer te geven en te wijzigen.

Backup Explorer heeft een C#-bibliotheek voor ervaren gebruikers. U kunt de bibliotheek in de toepassing rechtstreeks gebruiken om te werken met betrouw bare verzamelingen die vergelijkbaar zijn met de manier waarop klanten werken met de status manager in hun bestaande stateful Services. Voor basis gebruikers en in het geval van basis gebruik heeft de Explorer ook een zelfstandige REST-server die Api's beschikbaar maakt om de back-ups te controleren. Het bkpctl CLI-hulp programma werkt boven op de REST-Api's en is gebaseerd op python. U kunt het CLI-hulp programma gebruiken om back-ups te lezen en bij te werken, en om nieuwe back-ups te maken via de opdracht regel.

Zie voor meer informatie de Service Fabric opslag plaats github [back-up Verkenner](https://github.com/microsoft/service-fabric-backup-explorer) . De opslag plaats bevat informatie over de bron en release en installatie-instructies.

U kunt de opslag plaats ook lokaal bouwen en op back-ups werken.
 
De NuGet voor back-upverkenner (micro soft. ServiceFabric. ReliableCollectionBackup. parser) is beschikbaar op [nuget.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Volgende stappen

* Lees meer over [betrouw bare verzamelingen in Azure service Fabric stateful Services](service-fabric-reliable-services-reliable-collections.md).
* Bekijk [service Fabric aanbevolen procedures](service-fabric-best-practices-overview.md).
