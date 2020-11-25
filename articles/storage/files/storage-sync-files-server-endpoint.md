---
title: Een Azure File Sync server-eind punt toevoegen/verwijderen | Microsoft Docs
description: Meer informatie over het toevoegen of verwijderen van een server eindpunt met Azure File Sync. Een server eindpunt is een bepaalde locatie op een geregistreerde server, zoals een map op een server volume.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013935"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Een Azure File Sync server-eind punt toevoegen/verwijderen
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit doet u door uw Windows-servers te transformeren naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

Een *Server-eind punt* vertegenwoordigt een specifieke locatie op een *geregistreerde server*, zoals een map op een server volume of de hoofdmap van het volume. Meerdere server eindpunten kunnen bestaan op hetzelfde volume als hun naam ruimten niet overlappen (bijvoorbeeld F:\sync1 en F:\sync2) en elk eind punt wordt gesynchroniseerd met een unieke synchronisatie groep. U kunt beleid voor Cloud lagen afzonderlijk configureren voor elk server eindpunt. Als u een server locatie met een bestaande set bestanden als een server-eind punt aan een synchronisatie groep toevoegt, worden deze bestanden samengevoegd met andere bestanden die zich al op andere eind punten in de synchronisatie groep bevindt.

Zie [Azure file sync implementeren](storage-sync-files-deployment-guide.md) voor informatie over het implementeren van Azure File Sync End-to-end.

## <a name="prerequisites"></a>Vereisten
Als u een server eindpunt wilt maken, moet u eerst controleren of aan de volgende criteria wordt voldaan: 
- Op de server is Azure File Sync agent geïnstalleerd en deze is geregistreerd. Instructies voor het installeren van de Azure File Sync agent vindt u in het artikel [een server registreren of registratie ongedaan maken bij Azure file sync](storage-sync-files-server-registration.md) . 
- Zorg ervoor dat er een opslag synchronisatie service is geïmplementeerd. Zie [Azure file sync implementeren](storage-sync-files-deployment-guide.md) voor meer informatie over het implementeren van een opslag synchronisatie service. 
- Zorg ervoor dat er een synchronisatie groep is geïmplementeerd. Meer informatie over het [maken van een synchronisatie groep](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Zorg ervoor dat de server is verbonden met internet en dat Azure toegankelijk is. We gebruiken poort 443 voor alle communicatie tussen de server en onze service.

## <a name="add-a-server-endpoint"></a>Servereindpunt toevoegen
Als u een server eindpunt wilt toevoegen, gaat u naar de gewenste synchronisatie groep en selecteert u Server eindpunt toevoegen.

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

De volgende informatie is vereist onder **Server eindpunt toevoegen**:

- **Geregistreerde server**: de naam van de server of het cluster voor het maken van het server eindpunt op.
- **Pad**: het pad op de Windows-Server dat moet worden gesynchroniseerd als onderdeel van de synchronisatie groep.
- **Cloud lagen**: een schakel optie om Cloud lagen in of uit te scha kelen. Wanneer deze functie is ingeschakeld, worden bestanden door Cloud lagen *gelaagd* naar uw Azure-bestands shares. Hiermee converteert u on-premises bestands shares naar een cache, in plaats van een volledige kopie van de gegevensset, om u te helpen de efficiëntie van de ruimte op uw server te beheren.
- **Beschik bare volume ruimte**: de hoeveelheid beschik bare ruimte die moet worden gereserveerd op het volume waarop het server eindpunt zich bevindt. Als het volume beschik bare ruimte bijvoorbeeld is ingesteld op 50% op een volume met één server eindpunt, wordt er ongeveer de helft van de hoeveelheid gegevens gelaagd tot Azure Files. Ongeacht of Cloud lagen zijn ingeschakeld, heeft uw Azure-bestands share altijd een volledige kopie van de gegevens in de synchronisatie groep.

Selecteer **maken** om het server eindpunt toe te voegen. De bestanden in een naam ruimte van een synchronisatie groep worden nu gesynchroniseerd. 

## <a name="remove-a-server-endpoint"></a>Een server eindpunt verwijderen
Als u het gebruik van Azure File Sync voor een bepaald server eindpunt wilt stoppen, kunt u het server-eind punt verwijderen. 

> [!Warning]  
> Probeer geen problemen met synchronisatie, Cloud lagen of enig ander aspect van Azure File Sync op te lossen door het server eindpunt te verwijderen en opnieuw te maken, tenzij expliciet door een micro soft-Engineer wordt geïnstrueerd. Het verwijderen van een server eindpunt is een destructieve bewerking, en gelaagde bestanden binnen het server eindpunt worden niet opnieuw verbonden met hun locaties op de Azure-bestands share nadat het server-eind punt opnieuw is gemaakt, wat leidt tot synchronisatie fouten. Opmerking: gelaagde bestanden die zich buiten de naam ruimte van het server eindpunt bevinden, kunnen permanent verloren gaan. Er zijn mogelijk gelaagde bestanden aanwezig in uw server eindpunt, zelfs als Cloud lagen nooit zijn ingeschakeld.

Om ervoor te zorgen dat alle gelaagde bestanden worden ingetrokken voordat u het server eindpunt verwijdert, schakelt u Cloud lagen op het server eindpunt uit en voert u vervolgens de volgende Power shell-cmdlet uit om alle gelaagde bestanden binnen de naam ruimte van uw server eindpunt op te halen:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Als u opgeeft `-Order CloudTieringPolicy` , worden de meest recent gewijzigde bestanden eerst ingetrokken.
Andere optionele, maar nuttige para meters die u kunt overwegen:
* `-ThreadCount` Hiermee wordt bepaald hoeveel bestanden parallel kunnen worden ingetrokken.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroep bewerking wordt uitgevoerd voor een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen nieuwe pogingen en moet altijd worden gebruikt in combi natie met de vorige para meter.

> [!Note]  
> Als het lokale volume dat als host fungeert voor de server onvoldoende beschik bare ruimte heeft om alle gelaagde gegevens in te trekken, `Invoke-StorageSyncFileRecall` mislukt de cmdlet.  

Het server eindpunt verwijderen:

1. Navigeer naar de opslag synchronisatie service waar uw server is geregistreerd.
2. Navigeer naar de gewenste synchronisatie groep.
3. Verwijder het server eindpunt dat u wenst in de synchronisatie groep in de opslag synchronisatie service. U kunt dit doen door met de rechter muisknop te klikken op het relevante server eindpunt in het deel venster synchronisatie groep.

    ![Een server eindpunt verwijderen uit een synchronisatie groep](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Volgende stappen
- [Een server met Azure File Sync registreren of de registratie ervan opheffen](storage-sync-files-server-registration.md)
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
