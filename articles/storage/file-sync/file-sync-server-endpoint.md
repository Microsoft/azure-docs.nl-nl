---
title: Een eindpunt voor Azure File Sync server toevoegen of verwijderen | Microsoft Docs
description: Informatie over het toevoegen of verwijderen van een server-eindpunt met Azure File Sync. Een server-eindpunt is een bepaalde locatie op een geregistreerde server, zoals een map op een servervolume.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bcb346782b8d158dab5371a583c5a2576f0a09c9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796685"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Een server-Azure File Sync toevoegen of verwijderen
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit doet u door uw Windows-servers om te zetten in een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

Een *server-eindpunt vertegenwoordigt* een specifieke locatie op een geregistreerde *server,* zoals een map op een servervolume of de hoofdmap van het volume. Meerdere server eindpunten kunnen bestaan op hetzelfde volume als hun naamruimten niet overlappen (bijvoorbeeld F:\sync1 en F:\sync2) en elk eindpunt wordt gesynchroniseerd met een unieke synchronisatiegroep. U kunt beleidsregels voor cloudopslaglagen afzonderlijk configureren voor elk server-eindpunt. Als u een serverlocatie met een bestaande set bestanden als een server-eindpunt toevoegt aan een synchronisatiegroep, worden deze bestanden samengevoegd met andere bestanden die zich al op andere eindpunten in de synchronisatiegroep bevinden.

Zie [Implementatie van Azure File Sync](file-sync-deployment-guide.md) voor meer informatie over het implementeren Azure File Sync end-to-end.

## <a name="prerequisites"></a>Vereisten
Als u een server-eindpunt wilt maken, moet u eerst controleren of aan de volgende criteria wordt voldaan: 
- De server heeft de Azure File Sync agent geïnstalleerd en is geregistreerd. Instructies voor het installeren van Azure File Sync Agent vindt u in het artikel Een [server registreren/de](file-sync-server-registration.md) registratie van een server Azure File Sync. 
- Zorg ervoor dat er een opslagsynchronisatieservice is geïmplementeerd. Zie [How to deploy Azure File Sync](file-sync-deployment-guide.md) (Een opslagsynchronisatieservice implementeren) voor meer informatie over het implementeren van een opslagsynchronisatieservice. 
- Zorg ervoor dat er een synchronisatiegroep is geïmplementeerd. Meer informatie over [het maken van een synchronisatiegroep.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Zorg ervoor dat de server is verbonden met internet en dat Azure toegankelijk is. We gebruiken poort 443 voor alle communicatie tussen de server en onze service.

## <a name="add-a-server-endpoint"></a>Servereindpunt toevoegen
Als u een server-eindpunt wilt toevoegen, gaat u naar de gewenste synchronisatiegroep en selecteert u Server-eindpunt toevoegen.

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-server-endpoint/add-server-endpoint.png)

De volgende informatie is vereist onder **Server-eindpunt toevoegen:**

- **Geregistreerde server:** de naam van de server of het cluster om het server-eindpunt op te maken.
- **Pad:** het pad op de Windows Server dat moet worden gesynchroniseerd als onderdeel van de synchronisatiegroep.
- **Cloudopslaglagen:** een schakelknop voor het in- of uitschakelen van cloudopslaglagen. Wanneer deze functie is ingeschakeld, worden bestanden in *cloudlagen* op uw Azure-bestands shares in lagen opgeslagen. Hiermee converteert u on-premises bestands shares naar een cache, in plaats van een volledige kopie van de gegevensset, om u te helpen de efficiëntie van de ruimte op uw server te beheren.
- **Vrije ruimte voor het** volume: de hoeveelheid vrije ruimte die moet worden reserveren op het volume waarop het server-eindpunt zich bevindt. Als de vrije ruimte van het volume bijvoorbeeld is ingesteld op 50% op een volume met één server-eindpunt, wordt ongeveer de helft van de hoeveelheid gegevens in lagen opgeslagen Azure Files. Ongeacht of opslag in cloudlagen is ingeschakeld, heeft uw Azure-bestands share altijd een volledige kopie van de gegevens in de synchronisatiegroep.

Selecteer **Maken om** het server-eindpunt toe te voegen. De bestanden in een naamruimte van een synchronisatiegroep worden nu synchroon gehouden. 

## <a name="remove-a-server-endpoint"></a>Een server-eindpunt verwijderen
Als u het gebruik van Azure File Sync voor een bepaald server-eindpunt wilt stoppen, kunt u het server-eindpunt verwijderen. 

> [!Warning]  
> Probeer geen problemen met synchronisatie, opslag in cloudlagen of een ander aspect van Azure File Sync op te lossen door het server-eindpunt te verwijderen en opnieuw te maken, tenzij dit expliciet wordt geïnstrueerd door een Microsoft-engineer. Het verwijderen van een server-eindpunt is een destructieve bewerking en gelaagde bestanden binnen het server-eindpunt worden niet opnieuw verbonden met hun locaties op de Azure-bestands share nadat het server-eindpunt opnieuw is gemaakt, wat leidt tot synchronisatiefouten. Houd er ook rekening mee dat gelaagde bestanden die buiten de naamruimte van het server-eindpunt bestaan, permanent verloren kunnen gaan. Gelaagde bestanden kunnen zich binnen uw server-eindpunt zelfs als opslag in cloudlagen nooit is ingeschakeld.

Om ervoor te zorgen dat alle gelaagde bestanden worden ingeroepen voordat u het server-eindpunt verwijdert, schakelt u opslag in cloudlagen op het server-eindpunt uit en voert u vervolgens de volgende PowerShell-cmdlet uit om alle gelaagde bestanden binnen de naamruimte van het server-eindpunt in te roepen:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Als u `-Order CloudTieringPolicy` opgeeft, worden eerst de meest recent gewijzigde bestanden ingeroepen.
Andere optionele maar nuttige parameters om rekening mee te houden zijn:
* `-ThreadCount` bepaalt hoeveel bestanden parallel kunnen worden ingeroepen.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroeppoging wordt gedaan van een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen het opnieuw proberen terughalen van pogingen en moet altijd worden gebruikt in combinatie met de vorige parameter.

> [!Note]  
> Als het lokale volume dat als host voor de server wordt gehost onvoldoende vrije ruimte heeft om alle gelaagde gegevens terug te halen, mislukt de `Invoke-StorageSyncFileRecall` cmdlet.  

Het server-eindpunt verwijderen:

1. Navigeer naar de opslagsynchronisatieservice waar uw server is geregistreerd.
1. Navigeer naar de gewenste synchronisatiegroep.
1. Verwijder het wenste server-eindpunt in de synchronisatiegroep in de opslagsynchronisatieservice. Dit kan worden bereikt door met de rechtermuisknop op het relevante server-eindpunt in het deelvenster synchronisatiegroep te klikken.

    ![Een server-eindpunt verwijderen uit een synchronisatiegroep](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Volgende stappen
- [De registratie van een server bij Azure File Sync](file-sync-server-registration.md)
- [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
- [Azure File Sync bewaken](file-sync-monitoring.md)
