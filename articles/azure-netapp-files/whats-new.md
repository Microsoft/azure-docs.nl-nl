---
title: Wat is er nieuw in Azure NetApp Files | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de nieuwste functies en verbeteringen van Azure NetApp Files.
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
ms.topic: overview
ms.date: 03/11/2021
ms.author: b-juche
ms.openlocfilehash: 84213832e59c50c85d9731ced39bbce6264df47e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590905"
---
# <a name="whats-new-in-azure-netapp-files"></a>Wat is er nieuw in Azure NetApp Files

Azure NetApp Files wordt regelmatig bijgewerkt. In dit artikel wordt een overzicht gegeven van de nieuwste functies en verbeteringen. 

## <a name="march-2021"></a>2021 maart 

* [Automatisch wijzigen van het formaat van een replicatie doel volume voor meerdere regio's](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    In een replicatie relatie tussen regio's wordt een doel volume automatisch aangepast op basis van de grootte van het bron volume. U hoeft dus niet afzonderlijk het formaat van het doel volume te wijzigen. Dit gedrag bij het automatisch aanpassen van het formaat is van toepassing wanneer de volumes zich in een actieve replicatie relatie bevinden of wanneer replicatie peering is verbroken met de hersynchronisatie-bewerking. Deze functie werkt alleen als u voldoende ruimte hebt in de capaciteits groepen voor zowel de bron-als de doel volumes.

## <a name="december-2020"></a>December 2020

* [Het hulpprogramma Azure Application Consistent Snapshot](azacsnap-introduction.md) (openbare preview)    

    Het hulpprogramma Azure Application Consistent Snapshot Tool (AzAcSnap) is een opdrachtregelprogramma waarmee u gegevensbescherming voor databases van derden (SAP HANA) in Linux-omgevingen (bijvoorbeeld SUSE en RHEL) kunt vereenvoudigen.   

    AzAcSnap maakt gebruik van de functie voor momentopnames en replicatie van volumes in Azure NetApp Files en Azure grote instantie. Dit biedt de volgende voordelen:

    * Toepassingsconsistente gegevensbescherming 
    * Beheer van databasecatalogus 
    * *Ad-hoc* beveiliging van volumes 
    * Klonen van opslagvolumes 
    * Ondersteuning voor herstel na noodgeval 

## <a name="november-2020"></a>November 2020

* [Momentopname terugdraaien](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    Met de functie voor het terugdraaien van momentopnamen kunt u een volume snel herstellen naar de status waarin het zich bevond toen een bepaalde momentopname werd gemaakt. In de meeste gevallen is het herstellen van een volume veel sneller dan het herstellen van afzonderlijke bestanden van een momentopname naar het actieve bestandssysteem. Het is ook meer ruimte-efficiënt vergeleken met het herstellen van een momentopname naar een nieuw volume.

## <a name="september-2020"></a>September 2020

* [Replicatie tussen regio's van Azure NetApp Files](cross-region-replication-introduction.md) (openbare preview)

  Azure NetApp Files ondersteunt nu replicatie tussen regio's. Met deze nieuwe mogelijkheid voor herstel na noodgevallen kunt u Azure NetApp Files-volumes op een snelle en rendabele manier van de ene Azure-regio naar een andere repliceren. Zo worden uw gegevens beschermd tegen onvoorziene regionale storingen. Azure NetApp Files replicatie tussen regio's maakt gebruik van NetApp SnapMirror®-technologie; alleen gewijzigde blokken worden in een gecomprimeerde, efficiënte indeling via het netwerk verzonden. Deze bedrijfseigen technologie minimaliseert de hoeveelheid gegevens die nodig zijn replicatie tussen regio's uit te voeren, waardoor kosten voor gegevensoverdracht worden bespaard. Ook wordt de replicatietijd verkort, zodat u een kleinere RPO (Recovery Point Objective) kunt verwezenlijken.

* [Handmatige QoS-capaciteitspool](manual-qos-capacity-pool-introduction.md) (preview)  

    In een handmatige QoS-capaciteitspool kunt u de capaciteit en doorvoer voor een volume onafhankelijk toewijzen. De totale doorvoer van alle volumes die met een handmatige QoS-capaciteitspool zijn gemaakt, wordt beperkt door de totale doorvoer van de pool. Deze wordt bepaald door de combinatie van poolgrootte en de doorvoer op serviceniveau. Een capaciteitspool van het [QoS-type](azure-netapp-files-understand-storage-hierarchy.md#qos_types) kan ook 'auto' (automatisch) zijn. Dit is de standaardinstelling. In een auto-QoS-capaciteitspool wordt de doorvoer automatisch toegewezen aan de volumes in de pool. Dit is evenredig aan het quotum van de grootte dat aan de volumes is toegewezen.

* [LDAP-ondertekening](azure-netapp-files-create-volumes-smb.md) (preview)   

    Azure NetApp Files ondersteunt nu LDAP-ondertekening voor beveiligde LDAP-zoekacties tussen de Azure NetApp Files-service en de door de gebruiker opgegeven Active Directory Domain Services-domeincontrollers. Deze functie is momenteel beschikbaar als preview-product.

* [AES-versleuteling voor AD-verificatie](azure-netapp-files-create-volumes-smb.md) (preview)

    Azure NetApp Files ondersteunt nu AES-versleuteling voor LDAP-verbindingen met de domeincontrollers om AES-versleuteling voor een SMB-volume mogelijk te maken. Deze functie is momenteel beschikbaar als preview-product. 

* Nieuwe [metrische gegevens](azure-netapp-files-metrics.md):   

    * Nieuwe metrische gegevens van volumes: 
        * *Toegewezen grootten voor volumes*: De ingerichte grootte van een volume
    * Nieuwe metrische gegevens van elastische pools: 
        * *Toegewezen grootten voor pools*: De ingerichte grootte van de pool 
        * *Totale grootte van de momentopname voor de pool*: De som van de grootten van de momentopnamen van alle volumes in de pool

## <a name="july-2020"></a>Juli 2020

* [Volume van dubbel protocol (NFSv3 en SMB)](create-volumes-dual-protocol.md)

    U kunt nu een Azure NetApp Files-volume maken waarmee gelijktijdige toegang tot dubbele protocollen (NFS v3 en SMB) mogelijk is met ondersteuning voor toewijzing van LDAP-gebruikers. Deze functie maakt gebruik van use-cases met bijvoorbeeld een op Linux gebaseerde workload waarmee gegevens in een Azure NetApp Files-volume worden gegenereerd en opgeslagen. Daarnaast moet uw personeel op Windows gebaseerde clients en software gebruiken om de zojuist gegenereerde gegevens van hetzelfde Azure NetApp Files-volume te analyseren. De gelijktijdige toegang tot dubbele protocollen omzeilt de noodzaak om de door de workload gegenereerde gegevens te kopiëren naar een afzonderlijk volume met een ander protocol voor post-analyse, waarmee opslagkosten en operationele tijd kunnen worden bespaard. Deze functie is gratis (de gebruikelijke [kosten voor Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) zijn nog steeds van toepassing) en is algemeen beschikbaar. Meer informatie vindt u in de [documentatie over gelijktijdige toegang tot dubbele protocollen](create-volumes-dual-protocol.MD).

* [NFS v4.1 Kerberos-versleuteling 'in transit'](configure-kerberos-encryption.MD)

    Azure NetApp Files ondersteunt nu NFS-clientversleuteling in Kerberos-modi (krb5, krb5i en krb5p) met AES-256-versleuteling, wat u extra gegevensbeveiliging oplevert. Deze functie is gratis (de gebruikelijke [kosten voor Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) zijn nog steeds van toepassing) en is algemeen beschikbaar. Meer informatie vindt u in de [documentatie over NFS v4.1 Kerberos-versleuteling](configure-kerberos-encryption.MD).

* [Wijziging van het serviceniveau van het dynamisch volume](dynamic-change-volume-service-level.MD)

    De cloud belooft flexibiliteit in IT-uitgaven. U kunt nu het serviceniveau van een bestaand Azure NetApp Files-volume wijzigen door het volume te verplaatsen naar een andere capaciteitspool die gebruikmaakt van het gewenste serviceniveau voor het volume. Voor deze in-place wijziging van het serviceniveau voor het volume is niet vereist dat u gegevens migreert. Het is ook niet van invloed op de toegang van het gegevensvlak tot het volume. Als u de prestaties wilt verbeteren, kunt u een bestaand volume zodanig wijzigen dat het een hoger serviceniveau kan gebruiken. Als u de kosten wilt optimaliseren, kunt u het volume aanpassen om een lager serviceniveau te gebruiken. Deze functie is gratis (de gebruikelijke [kosten voor Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) zijn nog steeds van toepassing) en is momenteel in openbare preview. U kunt zich registreren voor de preview door de [documentatie over het wijzigen van het serviceniveau voor het dynamische volume](dynamic-change-volume-service-level.md) te raadplegen.

* [Beleid voor momentopnamen van volumes](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (preview) 

    Met Azure NetApp Files kunt u op bepaalde tijdstippen momentopnamen van uw volumes maken. U kunt nu een beleid voor momentopnamen maken zodat Azure NetApp Files automatisch momentopnamen van volumes maakt wanneer en zo vaak u dat wilt. U kunt inplannen dat de momentopnamen in cycli van een uur, een dag, een week of een maand moeten worden gemaakt. U kunt ook het maximum aantal momentopnamen opgeven dat moet worden bewaard als onderdeel van het momentopnamebeleid. Deze functie is gratis (de gebruikelijke [kosten voor Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) zijn nog steeds van toepassing) en is momenteel in preview. U kunt zich registreren voor de functiepreview door de [documentatie over het beleid voor momentopnamen van volumes](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) te raadplegen.

* [Exportbeleid voor toegang tot de NFS-hoofdmap](azure-netapp-files-configure-export-policy.md)

    In Azure NetApp Files kunt u nu opgeven of het account voor de hoofdmap toegang moet krijgen tot het volume. 

* [Pad van momentopname verbergen](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    In Azure NetApp Files kunt u nu opgeven of een gebruiker de `.snapshot`-map (NFS-clients) of `~snapshot`-map (SMB-clients) op een gekoppeld volume kan zien en openen.

## <a name="may-2020"></a>Mei 2020

* [Gebruikers van back-upbeleid](create-active-directory-connections.md) (preview)

    Met Azure NetApp Files kunt u aanvullende accounts opnemen waarvoor verhoogde bevoegdheden zijn vereist voor het computeraccount dat voor gebruik met Azure NetApp Files is gemaakt. Met de opgegeven accounts kunnen de NTFS-machtigingen op bestands- of mapniveau worden gewijzigd. U kunt bijvoorbeeld een niet-gemachtigd serviceaccount opgeven dat wordt gebruikt voor het migreren van gegevens naar een SMB-bestandsshare in Azure NetApp Files. De functie Gebruikers van back-upbeleid is momenteel als preview beschikbaar.

## <a name="next-steps"></a>Volgende stappen
* [Wat is Azure NetApp Files?](azure-netapp-files-introduction.md)
* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
