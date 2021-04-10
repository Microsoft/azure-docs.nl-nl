---
title: Volume replicatie maken voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u peering voor volume replicatie maakt voor Azure NetApp Files om replicatie tussen verschillende regio's in te stellen.
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
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 2a3c788ce50ccc1d537fd2903fe05acffd079b0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591007"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Volume replicatie voor Azure NetApp Files maken

> [!IMPORTANT]
> De functie voor replicatie van meerdere regio's is momenteel beschikbaar als open bare preview. U moet een Waitlist-aanvraag indienen om toegang te krijgen tot de functie via de [Waitlist-verzend pagina van Azure NetApp files cross-Region replicatie](https://aka.ms/anfcrrpreviewsignup). Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u de functie voor replicatie tussen regio's gebruikt.

In dit artikel wordt beschreven hoe u replicatie tussen regio's instelt door replicatie-peering te maken. 

Door replicatie-peering in te stellen, kunt u gegevens asynchroon repliceren van een Azure NetApp Files volume (bron) naar een ander Azure NetApp Files volume (bestemming). Het bron volume en het doel volume moeten in verschillende regio's worden geïmplementeerd. Het service niveau voor de doel capaciteits groep kan overeenkomen met die van de bron capaciteits groep of u kunt een ander service niveau selecteren.   

Azure NetApp Files replicatie biedt momenteel geen ondersteuning voor meerdere abonnementen. alle replicaties moeten worden uitgevoerd onder één abonnement.

Voordat u begint, moet u ervoor zorgen dat u de [vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)hebt gecontroleerd.  

## <a name="locate-the-source-volume-resource-id"></a>De resource-ID van het bron volume zoeken  

U moet de resource-ID ophalen van het bron volume dat u wilt repliceren. 

1. Ga naar het bron volume en selecteer **Eigenschappen** onder instellingen om de resource-id van het bron volume weer te geven.   
    ![Resource-ID van bron volume zoeken](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Kopieer de resource-ID naar het klem bord.  U hebt dit later nodig.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Het gegevens replicatie volume maken (het doel volume)

U moet een doel volume maken waarnaar u de gegevens van het bron volume wilt repliceren.  Voordat u een doel volume kunt maken, moet u een NetApp-account en een capaciteits groep in de doel regio hebben. 

1. Het doel account moet zich in een andere regio bevinden dan de regio van het bron volume. Als dat nodig is, maakt u een NetApp-account in de Azure-regio die moet worden gebruikt voor replicatie door de stappen in [een NetApp-account maken](azure-netapp-files-create-netapp-account.md)te volgen.   
U kunt ook een bestaand NetApp-account in een andere regio selecteren.  

2. Maak, indien nodig, een capaciteits pool in het zojuist gemaakte NetApp-account door de stappen in [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md)te volgen.   

    U kunt ook een bestaande capaciteits groep selecteren om het replicatie doel volume te hosten.  

    Het service niveau voor de doel capaciteits groep kan overeenkomen met die van de bron capaciteits groep of u kunt een ander service niveau selecteren.

3. Delegeer een subnet in de regio die moet worden gebruikt voor replicatie door de stappen in [een subnet delegeren aan Azure NetApp files](azure-netapp-files-delegate-subnet.md).

4. Maak het gegevens replicatie volume door **volumes** te selecteren onder Storage-service in het doel-NetApp-account. Klik vervolgens op de knop **+ gegevens replicatie toevoegen** .  

    ![Gegevens replicatie toevoegen](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Op de pagina een volume maken dat wordt weer gegeven, voert u de volgende velden in op het tabblad **basis beginselen** :
    * Volumenaam
    * Capaciteits pool
    * Volume quota
        > [!NOTE] 
        > De volume quota (grootte) voor het doel volume moet mirror zijn van het bron volume. Als u een grootte opgeeft die kleiner is dan het bron volume, wordt het doel volume automatisch aangepast aan de grootte van het bron volume. 
    * Virtueel netwerk 
    * Subnet

    Zie [een NFS-volume maken](azure-netapp-files-create-volumes.md#create-an-nfs-volume)voor meer informatie over de velden. 

6. Selecteer op het tabblad **protocol** hetzelfde protocol als het bron volume.  
Zorg ervoor dat voor het NFS-protocol de export beleids regels voldoen aan de vereisten van hosts in het externe netwerk die toegang tot de export hebben.  

7. Op het tabblad **labels** maakt u waar nodig sleutel/waarde-paren.  

8. Plak onder het tabblad **replicatie** in de bron volume bron-id die u hebt verkregen in [Zoek de bron-id van het resource volume](#locate-the-source-volume-resource-id)en selecteer vervolgens het gewenste replicatie schema. Opties voor replicatie schema zijn: elke 10 minuten, elk uur, dagelijks, wekelijks en maandelijks.  

    ![Volumereplicatie maken](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Klik op **controleren + maken** en klik vervolgens op **maken** om het volume voor gegevens replicatie te maken.   

    ![Replicatie controleren en maken](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Replicatie van het bron volume autoriseren  

Als u de replicatie wilt autoriseren, moet u de bron-ID van het replicatie doel volume verkrijgen en plakken in het veld autoriseren van het replicatie bron volume. 

1. Ga in het Azure Portal naar Azure NetApp Files.

2. Ga naar het doel-NetApp en de doel capaciteits groep waar het replicatie doel volume zich bevindt.

3. Selecteer het replicatie doel volume, ga naar **Eigenschappen** onder instellingen en zoek de **bron-id** van het doel volume. Kopieer de bron-ID van het doel volume naar het klem bord.

    ![Bron-ID van eigenschappen](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Ga in Azure NetApp Files naar het bron account voor replicatie en de bron capaciteits groep. 

5. Zoek het replicatie bron volume en selecteer deze. Ga naar **replicatie** onder Storage service en klik op **autoriseren**.

    ![Replicatie autoriseren](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. Plak in het veld autoriseren de bron-ID van het doel replicatie volume dat u hebt verkregen in stap 3 en klik vervolgens op **OK**.

## <a name="next-steps"></a>Volgende stappen  

* [Replicatie in meerdere regio's](cross-region-replication-introduction.md)
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Metrische gegevens van de volume replicatie](azure-netapp-files-metrics.md#replication)
* [Herstel na noodgevallen beheren](cross-region-replication-manage-disaster-recovery.md)
* [Volumereplicaties of volumes verwijderen](cross-region-replication-delete.md)
* [Problemen met kruis regio's oplossen-replicatie](troubleshoot-cross-region-replication.md)

