---
title: Gevolgen van de prestaties van Kerberos op Azure NetApp Files NFSv 4.1-volumes | Microsoft Docs
description: Hierin worden de beschik bare beveiligings opties, de geteste prestatie vectoren en de verwachte invloed op de prestaties van Kerberos op Azure NetApp Files NFSv 4.1-volumes beschreven.
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746090"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Invloed op de prestaties van Kerberos op Azure NetApp Files 4.1-volumes

Azure NetApp Files ondersteunt [NFS-client versleuteling in Kerberos](configure-kerberos-encryption.md) -modi (krb5, krb5i en krb5p) met AES-256-versleuteling. In dit artikel wordt de invloed op de prestaties van Kerberos op NFSv 4.1-volumes beschreven. 

## <a name="available-security-options"></a>Beschik bare beveiligings opties 

De volgende beveiligings opties zijn beschikbaar voor NFSv 4.1-volumes: 

* **SEC = sys** gebruikt lokale Unix-Uid's zijn en gids met behulp van AUTH_SYS om NFS-bewerkingen te verifiëren.
* **SEC = krb5** gebruikt Kerberos V5 in plaats van lokale Unix-Uid's zijn en gids om gebruikers te verifiëren.
* **SEC = krb5i** gebruikt Kerberos V5 voor gebruikers verificatie en voert integriteits controle van NFS-bewerkingen uit met behulp van beveiligde controle sommen om te voor komen dat gegevens worden geknoeid.
* **SEC = krb5p** gebruikt Kerberos V5 voor gebruikers verificatie en integriteits controle. Het NFS-verkeer wordt versleuteld om te voor komen dat verkeer wordt gesniffd. Deze optie is de veiligste instelling, maar het omvat ook de meeste prestatie overhead.

## <a name="performance-vectors-tested"></a>Prestatie vectoren getest

In deze sectie wordt de invloed op de prestaties van één client-side van de verschillende `sec=*` opties beschreven.

* De invloed op de prestaties is getest op twee niveaus: lage gelijktijdigheid (lage belasting) en hoge gelijktijdigheid van I/O en door voer.  
* Er zijn drie typen werk belastingen getest:  
    * Kleine bewerking wille keurige Lees-en schrijf opdracht (met behulp van FIO)
    * Grote bewerking sequentiële Lees-en schrijf bewerkingen (met behulp van FIO)
    * Zware werk belasting voor meta gegevens zoals gegenereerd door toepassingen zoals git

## <a name="expected-performance-impact"></a>Verwachte prestatie-impact 

Er zijn twee focus gebieden: lichte belasting en bovengrens. De volgende lijst bevat een beschrijving van de beveiligings instellingen voor de prestaties van de beveiligings instelling en het scenario per scenario. Alle vergelijkingen worden gemaakt op basis van de `sec=sys` beveiligings parameter. De test is uitgevoerd op één volume, met behulp van één client. 

Prestatie-impact van krb5:

* Lage gelijktijdigheid (r/w):
    * Sequentiële latentie steeg 0,3 MS.
    * Een wille keurige I/O-latentie steeg 0,2 MS.
    * I/O-latentie van meta gegevens 0,2 MS.
* Hoge gelijktijdigheid (r/w): 
    * De maximale sequentiële door Voer is onbeïnvloed door krb5.
    * Het maximale aantal wille keurige I/O-bewerkingen worden verminderd met 30% voor zuivere Lees bewerkingen, waarbij de totale impact op nul wordt verplaatst, omdat de werk belasting verschuift naar puur geschreven. 
    * De maximale hoeveelheid werk belasting van de meta gegevens is 30% verlaagd.

Prestatie-impact van krb5i: 

* Lage gelijktijdigheid (r/w):
    * Sequentiële latentie steeg 0,5 ms.
    * Een wille keurige I/O-latentie steeg 0,2 MS.
    * I/O-latentie van meta gegevens 0,2 MS.
* Hoge gelijktijdigheid (r/w): 
    * De maximale sequentiële door Voer is verminderd met 70% in het algemeen, ongeacht het mengsel van de werk belasting.
    * Het maximale aantal wille keurige I/O wordt verlaagd met 50% voor zuivere Lees bewerkingen, waarbij de totale impact afneemt tot 25% naarmate de werk belasting verschuift naar pure schrijf bewerking. 
    * De maximale hoeveelheid werk belasting van de meta gegevens is 30% verlaagd.

Prestatie-impact van krb5p:

* Lage gelijktijdigheid (r/w):
    * Sequentiële latentie steeg 0,8 ms.
    * Een wille keurige I/O-latentie steeg 0,2 MS.
    * I/O-latentie van meta gegevens 0,2 MS.
* Hoge gelijktijdigheid (r/w): 
    * De maximale sequentiële door Voer is verminderd met 85% in het algemeen, ongeacht het mengsel van de werk belasting. 
    * Het maximale aantal wille keurige I/O wordt verlaagd met 65% voor zuivere Lees bewerkingen, waarbij de totale impact afneemt tot 43%, omdat de werk belasting verschuift naar pure schrijf bewerking. 
    * De maximale hoeveelheid werk belasting van de meta gegevens is 30% verlaagd.

## <a name="next-steps"></a>Volgende stappen  

* [NFSv4.1 Kerberos-versleuteling configureren voor Azure NetApp Files](configure-kerberos-encryption.md) 
