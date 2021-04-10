---
title: Problemen met kruis regio's oplossen-replicatie voor Azure NetApp Files | Microsoft Docs
description: Hierin worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met replicatie tussen regio's voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590922"
---
# <a name="troubleshoot-cross-region-replication"></a>Problemen met replicatie tussen regio's oplossen

In dit artikel worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met replicatie tussen regio's voor Azure NetApp Files. 

## <a name="errors-creating-replication"></a>Fouten bij het maken van de replicatie  

|     Foutbericht    |     Oplossing    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     U kunt geen replicatie maken met een bron volume dat al een gegevens replicatie relatie heeft.    |
|     `Peered region   '{0}' is not accepted`    |     U probeert een replicatie tussen niet-peered regio's te maken.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Controleer of de ID van de externe resource een volume Resource-ID is.    |

## <a name="errors-authorizing-volume"></a>Fouten bij het autoriseren van het volume  

|     Foutbericht    |     Oplossing    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     De   `RemoteResourceID` ontbreekt of is ongeldig vanuit de gebruikers interface of API-aanvraag (fout bericht oplossen).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     De   `RemoteResourceID` ontbreekt of is ongeldig vanuit de gebruikers interface of API-aanvraag.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Valideren   `RemoteResourceID` of het juist is of bestaat voor de gebruiker.    |
|     `Remote volume   '{0}' is not configured for replication`    |     Het doel volume is geen gegevens beveiligings volume.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Het gegevens beveiligings volume heeft dit bron volume niet in de externe Resource-ID (de verkeerde bron-ID is opgegeven).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Deze fout geeft aan dat er een server fout is opgetreden. Neem contact op met ondersteuning.    |

## <a name="errors-deleting-replication"></a>Fouten bij het verwijderen van de replicatie

|     Foutbericht    |     Oplossing    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Controleer of replicatie is verbroken of niet is geïnitialiseerd en niet-actief is (de initialisatie is mislukt).    |
|     `Cannot delete   source replication`    |     Het verwijderen van de replicatie vanaf de bron kant is niet toegestaan. Zorg ervoor dat u de replicatie van de doel zijde verwijdert.    |

## <a name="errors-deleting-volume"></a>Fouten bij het verwijderen van het volume

|     Foutbericht    |     Oplossing    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  Verwijder replicatie voordat u het volume verwijdert. Zie [replicaties verwijderen](cross-region-replication-delete.md). Voor deze bewerking moet u de peering verbreekt voordat de replicatie voor het volume wordt verwijderd. |
| `Volume with replication cannot be deleted`  |  Verwijder replicatie voordat u het volume verwijdert. Zie [replicaties verwijderen](cross-region-replication-delete.md). Voor deze bewerking moet u de peering verbreekt voordat de replicatie voor het volume wordt verwijderd. 

## <a name="errors-resyncing-volume"></a>Fouten bij het opnieuw synchroniseren van volume

|     Foutbericht    |     Oplossing    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Controleer of de status van de volume replicatie is beschadigd.    |

## <a name="errors-deleting-snapshot"></a>Fouten bij het verwijderen van de moment opname 

|     Foutbericht    |     Oplossing    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Controleer of de replicatie van het volume is verbroken als u deze moment opname wilt verwijderen.    |
|     `Cannot delete   volume replication generated snapshot`    |     Het verwijderen van moment opnamen van de replicatie basislijn is niet toegestaan.    |

## <a name="errors-resizing-volumes"></a>Fouten bij het wijzigen van de grootte van volumes

|     Foutbericht    |     Oplossing    |
|-|-|
|   Het wijzigen van het formaat van het bron volume is mislukt met de fout `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  Zorg ervoor dat u voldoende ruimte hebt in de capaciteits groepen voor zowel de bron-als de doel volumes van replicatie tussen regio's. Wanneer u de grootte van het bron volume wijzigt, wordt het doel volume automatisch gewijzigd. Maar als de capaciteits pool die het doel volume host niet voldoende ruimte heeft, mislukt het wijzigen van de grootte van zowel de bron-als de doel volumes. Zie [het formaat wijzigen van een replicatie doel volume voor meerdere regio's](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) voor meer informatie.   |

## <a name="next-steps"></a>Volgende stappen  

* [Replicatie in meerdere regio's](cross-region-replication-introduction.md)
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Volumereplicatie maken](cross-region-replication-create-peering.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Herstel na noodgevallen beheren](cross-region-replication-manage-disaster-recovery.md)
* [Het formaat van een replicatie doel volume voor meerdere regio's wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Problemen met replicatie tussen regio's oplossen](troubleshoot-cross-region-replication.md)
