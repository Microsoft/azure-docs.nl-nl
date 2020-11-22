---
title: Vereisten en overwegingen voor het gebruik van Azure NetApp Files volume replicatie tussen regio's | Microsoft Docs
description: Hierin worden de vereisten en overwegingen beschreven voor het gebruik van de replicatie functionaliteit voor meerdere regio's van Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 7b664dcd1cb12808960ffacf91c6d02d58632c4e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243134"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Vereisten en overwegingen voor het gebruik van replicatie tussen regio's 

Houd rekening met de volgende vereisten en overwegingen voor [het gebruik van de functionaliteit voor replicatie van meerdere regio's](cross-region-replication-create-peering.md) van Azure NetApp files:  

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen 

* De functie voor replicatie van meerdere regio's is momenteel beschikbaar als open bare preview. U moet een Waitlist-aanvraag indienen om toegang te krijgen tot de functie via de [Waitlist-verzend pagina van Azure NetApp files cross-Region replicatie](https://aka.ms/anfcrrpreviewsignup). Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u de functie voor replicatie tussen regio's gebruikt.
* Azure NetApp Files replicatie is alleen beschikbaar in bepaalde paren met vaste regio's. Zie [ondersteunde regio paren](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB-volumes worden samen met NFS-volumes ondersteund. Voor de replicatie van SMB-volumes is een Active Directory verbinding vereist in de bron-en doel-NetApp-accounts. De doel-AD-verbinding moet toegang hebben tot de DNS-servers of domein controllers toevoegen die bereikbaar zijn vanuit het overgedragen subnet in de doel regio. Zie [vereisten voor Active Directory verbindingen](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)voor meer informatie. 
* Het doel account moet zich in een andere regio bevinden dan de regio van het bron volume. U kunt ook een bestaand NetApp-account in een andere regio selecteren.  
* Azure NetApp Files replicatie biedt momenteel geen ondersteuning voor meerdere abonnementen. alle replicaties moeten worden uitgevoerd onder één abonnement.
* U kunt Maxi maal vijf volumes voor replicatie instellen binnen één abonnement per regio. U kunt een ondersteunings ticket openen om een verhoging van het standaard quotum van vijf replicatie doel volumes (per abonnement in een regio) aan te vragen. 
* Er kan een vertraging van Maxi maal vijf minuten zijn voor de interface om een nieuwe toegevoegde moment opname op het bron volume weer te geven.  
* Trapsgewijze topologieën en ventilatoren worden niet ondersteund.
* Het configureren van de volume replicatie voor bron volumes die zijn gemaakt op basis van een moment opname, wordt op dit moment niet ondersteund.
* Nadat u de replicatie van meerdere regio's hebt ingesteld, maakt het replicatie proces *snapmirror-moment opnamen* om verwijzingen tussen het bron volume en het doel volume op te geven. Snapmirror-moment opnamen worden automatisch gerecycled wanneer er een nieuwe wordt gemaakt voor elke incrementele overdracht. U kunt snapmirror-moment opnamen pas verwijderen als de replicatie relatie en het volume zijn verwijderd. 
* U kunt hand matige moment opnamen verwijderen van het bron volume van een replicatie relatie wanneer de replicatie relatie actief of verbroken is, en ook nadat de replicatie relatie is verwijderd. U kunt hand matige moment opnamen voor het doel volume pas verwijderen als de replicatie relatie is verbroken.
* Het is niet mogelijk om terug te keren naar een moment opname die is gemaakt voordat het replicatie doel volume werd aangemaakt.

## <a name="next-steps"></a>Volgende stappen
* [Volume replicatie maken](cross-region-replication-create-peering.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Herstel na noodgevallen beheren](cross-region-replication-manage-disaster-recovery.md)
* [Metrische gegevens van de volume replicatie](azure-netapp-files-metrics.md#replication)
* [Volume replicaties of volumes verwijderen](cross-region-replication-delete.md)
* [Problemen met replicatie tussen regio's oplossen](troubleshoot-cross-region-replication.md)


