---
title: Resourcelimieten voor Azure NetApp Files | Microsoft Docs
description: Beschrijft limieten voor Azure NetApp Files resources en hoe u een verhoging van de resourcelimiet kunt aanvragen.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750190"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resourcelimieten voor Azure NetApp Files

Inzicht in resourcelimieten voor Azure NetApp Files helpt u bij het beheren van uw volumes.

## <a name="resource-limits"></a>Bronlimieten

De volgende tabel beschrijft resourcelimieten voor Azure NetApp Files:

|  Resource  |  Standaardlimiet  |  Aanpasbaar via ondersteuningsaanvraag  |
|----------------|---------------------|--------------------------------------|
|  Aantal NetApp-accounts per Azure-regio per abonnement  |  10    |  Yes   |
|  Aantal capaciteitspools per NetApp-account   |    25     |   Yes   |
|  Aantal volumes per abonnement   |    500     |   Yes   |
|  Aantal volumes per capaciteitspool     |    500   |    Yes     |
|  Aantal momentopnamen per volume       |    255     |    No        |
|  Aantal subnetten dat is gedelegeerd aan Azure NetApp Files (Microsoft.NetApp/volumes) per Azure-Virtual Network    |   1   |    No    |
|  Aantal gebruikte IP's in een VNet (inclusief onmiddellijk peered VNets) met Azure NetApp Files   |    1000   |    No   |
|  Minimale grootte van één capaciteitspool   |  4 TiB     |    No  |
|  Maximale grootte van één capaciteitspool    |  500 TiB   |   No   |
|  Minimale grootte van één volume    |    100 GiB    |    Nee    |
|  Maximale grootte van één volume     |    100 TiB    |    No    |
|  Maximale grootte van één bestand     |    16 TiB    |    No    |    
|  Maximale grootte van mapmetagegevens in één map      |    320 MB    |    No    |    
|  Maximum aantal bestanden ([maxfiles](#maxfiles)) per volume     |    100 miljoen    |    Yes    |    
|  Maximumaantal exportbeleidsregels per volume     |    5  |    No    | 
|  Minimale toegewezen doorvoer voor een handmatig QoS-volume     |    1 MiB/s   |    No    |    
|  Maximale toegewezen doorvoer voor een handmatig QoS-volume     |    4500 MiB/s    |    No    |    
|  Aantal replicatievolumes voor replicatie tussen regio's (doelvolumes)     |    5    |    Ja    |     

Als u wilt zien of een map de maximale groottelimiet voor mapmetagegevens (320 MB) nadert, gaat u naar Hoe kan ik bepalen of een map de [limietgrootte nadert.](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)   

Zie Veelgestelde vragen over [capaciteitsbeheer voor meer informatie.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Limieten voor maxfiles <a name="maxfiles"></a> 

Azure NetApp Files volumes hebben een limiet met de *naam maxfiles.* De limiet voor maxfiles is het aantal bestanden dat een volume kan bevatten. Linux-bestandssystemen verwijzen naar de limiet als *inodes*. De maximale bestandslimiet voor een Azure NetApp Files volume wordt geïndexeerd op basis van de grootte (quotum) van het volume. De limiet voor het maximum aantal bestanden voor een volume neemt toe of af met een snelheid van 20 miljoen bestanden per TiB van de inrichtende volumegrootte. 

De service past de limiet voor maxfiles voor een volume dynamisch aan op basis van de ingerichte grootte. Een volume dat in eerste instantie is geconfigureerd met een grootte van 1 TiB, heeft bijvoorbeeld een limiet van 20 miljoen bestanden. Volgende wijzigingen in de grootte van het volume zouden resulteren in een automatische aanpassing van de limiet voor maxfiles op basis van de volgende regels: 

|    Volumegrootte (quotum)     |  Automatische aanpassing van de limiet voor maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 miljoen     |
|    > 1 TiB, maar <= 2 TiB    |    40 miljoen     |
|    > 2 TiB, maar <= 3 TiB    |    60 miljoen     |
|    > 3 TiB, maar <= 4 TiB    |    80 miljoen     |
|    > 4 TiB                 |    100 miljoen    |

Als u al ten minste 4 TiB quotum voor een [](#limit_increase) volume hebt toegewezen, kunt u een ondersteuningsaanvraag indienen om de limiet voor maxfiles (inodes) te verhogen tot meer dan 100 miljoen. Voor elke 100 miljoen bestanden die u verhoogt (of een fractie daarvan), moet u het bijbehorende volumequotum verhogen met 4 TiB.  Als u bijvoorbeeld de limiet voor het maximumaantal bestanden verhoogt van 100 miljoen bestanden naar 200 miljoen bestanden (of een getal ertussen), moet u het volumequotum verhogen van 4 TiB naar 8 TiB.

U kunt de limiet voor maximumbestandslimiet verhogen naar 500 miljoen als uw volumequotum ten minste 20 TiB is. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Verhoging van aanvraaglimiet <a name="limit_increase"></a> 

U kunt een aanvraag ondersteuning voor Azure de aanpasbare limieten uit de bovenstaande tabel te verhogen. 

Vanuit Azure Portal navigatievlak: 

1. Klik **op Help en ondersteuning.**
2. Klik **op + Nieuwe ondersteuningsaanvraag.**
3. Geef op het tabblad Basisbeginselen u de volgende gegevens op: 
    1. Type probleem: Selecteer **Service- en abonnementslimieten (quota).**
    2. Abonnementen: selecteer het abonnement voor de resource dat u het quotum wilt verhogen.
    3. Quotumtype: Selecteer **Opslag: Azure NetApp Files limieten.**
    4. Klik **op Volgende: Oplossingen**.
4. Op het tabblad Details:
    1. Geef in het vak Beschrijving de volgende informatie op voor het bijbehorende resourcetype:

        |  Resource  |    Bovenliggende resources      |    Aangevraagde nieuwe limieten     |    Reden voor verhoging van quotum       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *Abonnements-id*   |  *Nieuw **maximumaccountnummer** aangevraagd*    |  *Welk scenario of gebruiksscenario heeft om de aanvraag gevraagd?*  |
        |  Pool    |  *Abonnements-id, NetApp-account-URI*  |  *Nieuw maximum aantal **pool** aangevraagd*   |  *Welk scenario of gebruiksscenario heeft om de aanvraag gevraagd?*  |
        |  Volume  |  *Abonnements-id, NetApp-account-URI, capaciteitspool-URI*   |  *Nieuw maximumvolume **aangevraagd***     |  *Welk scenario of gebruiksscenario heeft om de aanvraag gevraagd?*  |
        |  Maxfiles  |  *Abonnements-id, NetApp-account-URI, capaciteitspool-URI, volume-URI*   |  *Nieuw maximumaantal **maxfiles aangevraagd***     |  *Welk scenario of gebruiksscenario heeft om de aanvraag gevraagd?*  |    
        |  Gegevensbeveiligingsvolumes voor replicatie tussen regio's  |  *Abonnements-id, doel-NetApp-account-URI, URI van doelcapaciteitspool, bron-NetApp-account-URI, broncapaciteitsgroep-URI, bronvolume-URI*   |  *Nieuw maximum aantal replicatievolumes voor replicatie tussen **regio's aangevraagd (doelvolumes)** _     |  _What scenario of use-case om de aanvraag gevraagd?*  |    

    2. Geef de juiste ondersteuningsmethode op en geef uw contractgegevens op.

    3. Klik **op Volgende: Beoordelen en maken om** de aanvraag te maken. 


## <a name="next-steps"></a>Volgende stappen  

- [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md)
