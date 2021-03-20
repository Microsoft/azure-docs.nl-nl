---
title: Veelvoorkomende code pakket fouten vaststellen met behulp van Service Fabric
description: Meer informatie over het oplossen van algemene code pakket fouten met Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591712"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Veelvoorkomende code pakket fouten vaststellen met behulp van Service Fabric

In dit artikel wordt beschreven wat het betekent dat een code pakket onverwacht wordt beëindigd. Het biedt inzicht in mogelijke oorzaken van veelvoorkomende fout codes, samen met stappen voor probleem oplossing.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Wanneer wordt onverwacht een proces of container beëindigd?

Wanneer Azure Service Fabric een aanvraag voor het starten van een code pakket ontvangt, begint het met het voorbereiden van de omgeving op het lokale systeem op basis van de opties die zijn ingesteld in de app-en service manifesten. Deze voor bereidingen zijn onder andere het reserveren van netwerk eindpunten of bronnen, het configureren van firewall regels of het instellen van de beperkingen van resource beheer. 

Nadat de omgeving goed is geconfigureerd, probeert Service Fabric het code pakket op te halen. Deze stap wordt als geslaagd beschouwd als het besturings systeem of de container-runtime meldt dat het proces of de container is geactiveerd. Als de activering mislukt, ziet u een status bericht in SFX dat lijkt op het volgende:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Nadat het code pakket is geactiveerd, begint Service Fabric de levens duur te controleren. Op dit punt kan een proces of container op elk gewenst moment worden beëindigd om een aantal redenen. Het kan bijvoorbeeld zijn dat de initialisatie van een DLL-bestand is mislukt of dat het besturings systeem geen heap-ruimte op het bureau blad heeft. Als uw code pakket is beëindigd, ziet u het volgende status bericht in SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

De afsluit code in dit status bericht is de enige aanwijzingen dat het proces of de container over de oorzaak is van het afgebroken. Het kan worden gegenereerd door een wille keurig niveau van de stack. Deze afsluit code kan bijvoorbeeld betrekking hebben op een besturingssysteem fout of een .NET-probleem of door de code zijn gegenereerd. Gebruik dit artikel als uitgangs punt voor het vaststellen van de bron van afsluit afsluitings codes en mogelijke oplossingen. Houd er rekening mee dat dit algemene oplossingen zijn voor algemene scenario's en dat deze mogelijk niet van toepassing zijn op de fout die u ziet.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hoe kan ik zien of Service Fabric mijn code pakket heeft beëindigd?

Service Fabric kan verantwoordelijk zijn voor het beëindigen van uw code pakket om verschillende redenen. Het kan bijvoorbeeld besluiten het code pakket op een ander knoop punt te plaatsen voor taak verdeling. U kunt controleren of Service Fabric uw code pakket heeft beëindigd als u een van de afsluit codes in de volgende tabel ziet.

>[!NOTE]
> Als uw proces of container wordt beëindigd met een andere afsluit code dan de codes in de volgende tabel, is Service Fabric niet verantwoordelijk voor het beëindigen ervan.

Afsluitcode | Beschrijving
--------- | -----------
7147 | Hiermee wordt aangegeven dat het proces of de container door Service Fabric op de juiste wijze is afgesloten door een CTRL + C-signaal te verzenden.
7148 | Geeft aan dat het proces of de container is beëindigd Service Fabric. Deze fout code geeft soms aan dat het proces of de container niet tijdig heeft gereageerd na het verzenden van een CTRL + C-signaal en dat het is beëindigd.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Andere veelvoorkomende fout codes en mogelijke oplossingen

Afsluitcode | Hexadecimale waarde | Korte beschrijving | Hoofdoorzaak | Mogelijke oplossing
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Deze fout betekent soms dat de computer geen heap-ruimte op het bureau blad heeft. Dit is met name waarschijnlijk als u talrijke processen hebt die deel uitmaken van uw toepassing die op het knoop punt wordt uitgevoerd. | Als uw programma niet is gebouwd om te reageren op CTRL + C signalen, kunt u de instelling **EnableActivateNoWindow** inschakelen in het cluster manifest. Als u deze instelling inschakelt, wordt uw code pakket zonder GUI-venster uitgevoerd en worden er geen CTRL + C-signalen meer ontvangen. Met deze actie wordt ook de hoeveelheid beschik bare heap-ruimte op het bureau blad van elk proces verminderd. Als uw code pakket CTRL + C signalen moet ontvangen, kunt u de grootte van het bureau blad van het knoop punt verg Roten.
3762504530 | 0xe0434352 | N.v.t. | Deze waarde vertegenwoordigt de fout code voor een niet-verwerkte uitzonde ring van beheerde code (.NET). | Deze afsluit code geeft aan dat uw toepassing een uitzonde ring heeft gegenereerd die niet is verwerkt en waardoor het proces is beëindigd. Als eerste stap bij het bepalen van de activering van deze fout, fouten opsporen in de logboeken en dump bestanden van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [diagnosticeren van andere algemene scenario's](service-fabric-diagnostics-common-scenarios.md).
* Bekijk een gedetailleerd overzicht van Azure Monitor-logboeken en wat ze bieden door [Azure Monitor overzicht](../azure-monitor/overview.md)te lezen.
* Meer informatie over Azure Monitor- [Logboeken voor hulp bij detectie](../azure-monitor/alerts/alerts-overview.md) en diagnose.
* Krijg kennis met de functies voor [Zoeken in Logboeken en query's](../azure-monitor/logs/log-query-overview.md) die worden aangeboden als onderdeel van Azure monitor Logboeken.
