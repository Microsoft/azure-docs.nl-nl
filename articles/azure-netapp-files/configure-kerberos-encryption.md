---
title: NFSv 4.1 Kerberos-versleuteling configureren voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u NFSv 4.1 Kerberos-versleuteling configureert voor Azure NetApp Files en de invloed op de prestaties.
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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: 69168060cbce4a904c53d7f79895e909c8c42e01
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935220"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>NFSv 4.1 Kerberos-versleuteling voor Azure NetApp Files configureren

Azure NetApp Files ondersteunt NFS-client versleuteling in Kerberos-modi (krb5, krb5i en krb5p) met AES-256-versleuteling. In dit artikel worden de vereiste configuraties voor het gebruik van een NFSv 4.1-volume met Kerberos-versleuteling beschreven.

## <a name="requirements"></a>Vereisten

De volgende vereisten zijn van toepassing op NFSv 4.1 client Encryption: 

* Active Directory Domain Services (AD DS) verbinding maken om Kerberos-ticketing mogelijk te maken 
* DNS A/PTR-record maken voor de client-en Azure NetApp Files NFS-server IP-adressen
* Een Linux-client  
    Dit artikel bevat richt lijnen voor RHEL-en Ubuntu-clients.  Andere clients werken met vergelijk bare configuratie stappen. 
* NTP-server toegang  
    U kunt een van de veelgebruikte domein controllers voor de Active Directory-domein controller (AD DC) gebruiken.

## <a name="create-an-nfs-kerberos-volume"></a>Een NFS Kerberos-volume maken

1.  Volg de stappen in [een NFS-volume maken voor Azure NetApp files](azure-netapp-files-create-volumes.md) om het nfsv 4.1-volume te maken.   

    Stel op de pagina een volume maken de NFS-versie in op **nfsv 4.1** en stel Kerberos in op **ingeschakeld**.

    > [!IMPORTANT] 
    > U kunt de selectie van Kerberos-activering niet wijzigen nadat het volume is gemaakt.

    ![NFSv 4.1 Kerberos-volume maken](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Selecteer **beleid exporteren** om het gewenste niveau voor toegang en beveiliging (Kerberos 5, Kerberos 5i of Kerberos 5p) voor het volume overeen te laten komen.   

    Zie [prestaties beïnvloeden van Kerberos op nfsv 4.1](#kerberos_performance)voor de prestaties van Kerberos.  

    U kunt ook de Kerberos-beveiligings methoden voor het volume wijzigen door in het navigatie deel venster Azure NetApp Files op beleid exporteren te klikken.

3.  Klik op **controleren + maken** om het nfsv 4.1-volume te maken.

## <a name="configure-the-azure-portal"></a>Azure Portal configureren 

1.  Volg de instructies in [een Active Directory verbinding maken](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Voor Kerberos moet u ten minste één computer account maken in Active Directory. De account gegevens die u opgeeft, worden gebruikt voor het maken van de accounts voor zowel SMB- *als* Nfsv 4.1 Kerberos-volumes. De computer wordt automatisch gemaakt tijdens het maken van het volume.

2.  Voer onder **Kerberos-realm** de **naam** van de ad-server en het **IP-** adres van de KDC in.

    AD-server en KDC-IP kunnen dezelfde server zijn. Deze informatie wordt gebruikt om het SPN-computer account te maken dat wordt gebruikt door Azure NetApp Files. Nadat het computer account is gemaakt, gebruikt Azure NetApp Files DNS-server records om indien nodig aanvullende KDC-servers te vinden. 

    ![Kerberos-realm](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Klik op **toevoegen** om de configuratie op te slaan.

## <a name="configure-active-directory-connection"></a>Active Directory verbinding configureren 

Door de configuratie van NFSv 4.1 Kerberos worden twee computer accounts gemaakt in Active Directory:
* Een computer account voor SMB-shares
* Een computer account voor NFSv 4.1: u kunt dit account identificeren met behulp van het voor voegsel `NFS-` . 

Nadat u het eerste NFSv 4.1 Kerberos-volume hebt gemaakt, stelt u het versleutelings type voor het computer account in met behulp van de volgende Power shell-opdracht:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>De NFS-client configureren 

Volg de instructies in [een NFS-client configureren voor Azure NetApp files](configure-nfs-clients.md) om de NFS-client te configureren.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Het NFS Kerberos-volume koppelen

1. Selecteer op de pagina **volumes** het NFS-volume dat u wilt koppelen.

2. Selecteer **koppelings instructies** van het volume om de instructies weer te geven.

    Bijvoorbeeld: 

    ![Koppelings instructies voor Kerberos-volumes](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Maak de map (koppel punt) voor het nieuwe volume.  

4. Stel het standaard versleutelings type in op AES 256 voor het computer account:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * U hoeft deze opdracht slechts één keer uit te voeren voor elk computer account.
    * U kunt deze opdracht uitvoeren vanaf een domein controller of op een PC waarop [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) is geïnstalleerd. 
    * De `$NFSCOMPUTERACCOUNT` variabele is het computer account dat is gemaakt in Active Directory wanneer u het Kerberos-volume implementeert. Dit is het account dat wordt voorafgegaan door `NFS-` . 
    * De `$ANFSERVICEACCOUNT` variabele is een niet-privileged Active Directory gebruikers account met gedelegeerde besturings elementen voor de organisatie-eenheid waar het computer account is gemaakt. 

5. Koppel het volume op de host: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * De `$ANFEXPORT` variabele is het `host:/export` pad dat in de koppelings instructies is gevonden.
    * De `$ANFMOUNTPOINT` variabele is de map die door de gebruiker is gemaakt op de Linux-host.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Prestatie-impact van Kerberos op NFSv 4.1 

In deze sectie vindt u meer informatie over de invloed van de prestaties van Kerberos op NFSv 4.1.

### <a name="available-security-options"></a>Beschik bare beveiligings opties 

De volgende beveiligings opties zijn beschikbaar voor NFSv 4.1-volumes: 

* **SEC = sys** gebruikt lokale Unix-Uid's zijn en gids met behulp van AUTH_SYS om NFS-bewerkingen te verifiëren.
* **SEC = krb5** gebruikt Kerberos V5 in plaats van lokale Unix-Uid's zijn en gids om gebruikers te verifiëren.
* **SEC = krb5i** gebruikt Kerberos V5 voor gebruikers verificatie en voert integriteits controle van NFS-bewerkingen uit met behulp van beveiligde controle sommen om te voor komen dat gegevens worden geknoeid.
* **SEC = krb5p** gebruikt Kerberos V5 voor gebruikers verificatie en integriteits controle. Het NFS-verkeer wordt versleuteld om te voor komen dat verkeer wordt gesniffd. Deze optie is de veiligste instelling, maar het omvat ook de meeste prestatie overhead.

### <a name="performance-vectors-tested"></a>Prestatie vectoren getest

In deze sectie wordt de invloed op de prestaties van één client-side van de verschillende `sec=*` opties beschreven.

* De invloed op de prestaties is getest op twee niveaus: lage gelijktijdigheid (lage belasting) en hoge gelijktijdigheid van I/O en door voer.  
* Er zijn drie typen werk belastingen getest:  
    * Kleine bewerking wille keurige Lees-en schrijf opdracht (met behulp van FIO)
    * Grote bewerking sequentiële Lees-en schrijf bewerkingen (met behulp van FIO)
    * Zware werk belasting voor meta gegevens zoals gegenereerd door toepassingen zoals git

### <a name="expected-performance-impact"></a>Verwachte prestatie-impact 

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

* [Problemen met NFSv 4.1 Kerberos volume oplossen](troubleshoot-nfsv41-kerberos-volumes.md)
* [Veelgestelde vragen over Azure NetApp Files](azure-netapp-files-faqs.md)
* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Een Active Directory verbinding maken](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Een NFS-client voor Azure NetApp Files configureren](configure-nfs-clients.md) 
