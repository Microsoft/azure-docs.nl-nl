---
title: Azure Vm's HA voor SAP NW in Windows met Azure NetApp Files (SMB) | Microsoft Docs
description: Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's in Windows met Azure NetApp Files (SMB) voor SAP-toepassingen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: 90b0ab4fdabd40e803d1f85a640e4cb387e40c44
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958945"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's in Windows met Azure NetApp Files (SMB) voor SAP-toepassingen

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, configureert, het cluster raamwerk installeert en een Maxi maal beschik bare SAP NetWeaver 7,50-systeem op Windows-Vm's installeert met behulp van [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) op [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

De laag van de data base wordt in dit artikel niet uitvoerig besproken. We gaan ervan uit dat het [virtuele netwerk](../../../virtual-network/virtual-networks-overview.md) van Azure al is gemaakt.  

Lees eerst de volgende SAP-opmerkingen en-documenten:

* [Documentatie over Azure NetApp Files][anf-azure-doc] 
* SAP-opmerking [1928533][1928533], die het volgende bevat:  
  * Een lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste SAP-kernel-versie voor Windows op Microsoft Azure
* SAP-opmerking [2015553][2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2178632][2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [1999351][1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* SAP-opmerking [2287140](https://launchpad.support.sap.com/#/notes/2287140) bevat vereisten voor de SAP-ondersteunde CA-functie van het SMB 3. x-protocol.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) bevat informatie over het oplossen van problemen met de langzame uitvoering van SAP-transactie AL11 in Windows 2012 en 2016.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) bevat informatie over de transparante failover-functie voor een bestands share op Windows Server met het SMB 3,0-protocol.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) is een aanbeveling (het genereren van de naam van 8,3 namen) om te voor komen dat de prestaties/fouten van het bestands systeem worden vergemakkelijkt tijdens gegevens toegang.
* [Een hoge Beschik baarheid van SAP NetWeaver installeren op een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-exemplaren in azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines architectuur en scenario's met hoge Beschik baarheid voor SAP net-Weaver](./sap-high-availability-architecture-scenarios.md)
* [Test poort toevoegen in ASCS-cluster configuratie](sap-high-availability-installation-wsfc-file-share.md)
* [Installatie van een (A) SCS-exemplaar op een failovercluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Een SMB-volume maken voor Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

SAP heeft een nieuwe benadering en een alternatief voor gedeelde cluster schijven ontwikkeld voor het clusteren van een SAP ASCS/SCS-exemplaar op een Windows-failovercluster. In plaats van gedeelde cluster schijven kunt u met een SMB-bestands share SAP Global host-bestanden implementeren. Azure NetApp Files ondersteunt SMBv3 (samen met NFS) met NTFS ACL met behulp van Active Directory. Azure NetApp Files is automatisch Maxi maal beschikbaar (omdat het een PaaS-service is). Deze functies maken Azure NetApp Files uitstekende optie om de SMB-bestands share voor SAP Global te hosten.  
Zowel [Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) en [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) worden ondersteund. U kunt bestaande Active Directory domein controllers gebruiken met Azure NetApp Files. Domein controllers kunnen zich in azure bevindt als virtuele machines, of on-premises via ExpressRoute of S2S VPN. In dit artikel wordt een domein controller in een Azure-VM gebruikt.  
Voor hoge Beschik baarheid (HA) voor de SAP NetWeaver Central-Services is gedeelde opslag vereist. Om dat in Windows te bereiken, is het nood zakelijk dat u een SOFS-cluster maakt of een gedeelde cluster schijf gebruikt, zoals SIOS. Het is nu mogelijk om SAP NetWeaver HA te maken met behulp van gedeelde opslag, geïmplementeerd op Azure NetApp Files. Als u Azure NetApp Files voor de gedeelde opslag gebruikt, hoeft u SOFS of SIOS niet meer te gebruiken.  

> [!NOTE]
> Het clusteren van SAP ASCS/SCS-instanties met behulp van een bestands share wordt ondersteund voor SAP NetWeaver 7,40 (en hoger) met SAP kernel 7,49 (en hoger).  

![SAP ASCS/SCS HA-architectuur met SMB-share](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

De vereisten voor een SMB-bestands share zijn:
* SMB 3,0-protocol (of hoger).
* De mogelijkheid om Active Directory toegangs beheer lijsten (Acl's) in te stellen voor Active Directory gebruikers groepen en het computer object $ computer.
* De bestands share moet HA-ingeschakeld zijn.

De share voor de SAP Central-Services in deze referentie architectuur wordt aangeboden door Azure NetApp Files:

![SAP ASCS/SCS HA-architectuur met SMB-share Details](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Een SMB-volume voor Azure NetApp Files maken en koppelen

Voer de volgende stappen uit, als voor bereiding op het gebruik van Azure NetApp Files.  

1. Volg de stappen om u te [registreren voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Maak een Azure NetApp-account aan de hand van de stappen die worden beschreven in  [een NetApp-account maken](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Stel de capaciteits pool in en volg de instructies in [een capaciteits groep instellen](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Azure NetApp Files resources moeten zich in een gedelegeerd subnet bevinden. Volg de instructies in [een subnet overdragen aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) om het gedelegeerde subnet te maken.  

   > [!IMPORTANT]
   > U moet Active Directory verbindingen maken voordat u een SMB-volume maakt. Bekijk de [vereisten voor Active Directory verbindingen](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections).  

5. Active Directory verbinding maken, zoals beschreven in [een Active Directory-verbinding maken](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)  
6. SMB-Azure NetApp Files SMB-volume maken, volgens de instructies in [een SMB-volume toevoegen](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Koppel het SMB-volume op uw virtuele Windows-machine.

> [!TIP]
> U vindt de instructies voor het koppelen van het Azure NetApp Files volume, als u in [Azure Portal](https://portal.azure.com/#home) naar het Azure NetApp files-object navigeert, klikt u op de Blade **volumes** en **koppelt** u de instructies.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>De infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster 

1. [Stel de ASCS/SCS-taakverdelings regels in voor de interne Load Balancer van Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Virtuele Windows-machines toevoegen aan het domein](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Voeg register vermeldingen toe aan cluster knooppunten van het SAP ASCS/SCS-exemplaar](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Een Windows Server-failovercluster instellen voor een SAP ASCS/SCS-exemplaar](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Als u Windows Server 2016 gebruikt, raden we u aan Azure- [cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)te configureren.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>SAP ASCS-exemplaar op beide knoop punten installeren

U hebt de volgende software van SAP nodig:
   * SAP software Provisioning Manager (SWPM)-installatie programma versie SPS25 of hoger.
   * SAP kernel 7,49 of hoger
   * Maak een virtuele-hostnaam (cluster netwerk naam) voor het geclusterde SAP ASCS/SCS-exemplaar, zoals wordt beschreven in [een virtuele-hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Het clusteren van SAP ASCS/SCS-instanties met behulp van een bestands share wordt ondersteund voor SAP NetWeaver 7,40 (en hoger) met SAP kernel 7,49 (en hoger).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Een ASCS/SCS-exemplaar installeren op het eerste ASCS/SCS-cluster knooppunt

1. Installeer een SAP ASCS/SCS-exemplaar op het eerste cluster knooppunt. Start het SAP SWPM-installatie programma en navigeer naar: **product**  >  **DBMS** > installatie > Application Server ABAP (of Java) > High-Availability systeem > ASCS/SCS-exemplaar > eerste cluster knooppunt.  

2. Selecteer **Bestands share cluster** als de configuratie van de cluster share in SWPM.  
3. Wanneer u wordt gevraagd om stap **SAP-para meters** van het systeem, voert u de hostnaam in voor de Azure NetApp files SMB-share die u al hebt gemaakt als hostnaam van de **Bestands share**.  In dit voor beeld is de hostnaam van de SMB **-share anfsmb-9562**. 

   > [!IMPORTANT]
   > Als de SWPM-resultaten van de functie voor de voortdurende Beschik baarheid in de voor waarden van de voor gaande controle worden weer gegeven, kunt u dit probleem verhelpen door de instructies in [een vertraagde fout bericht te volgen wanneer u een gedeelde map probeert te openen die niet meer in Windows bestaat](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Als de controle van de vereisten in SWPM wordt weer gegeven, wordt niet voldaan aan de voor waarde voor wissel grootte, kunt u de wissel grootte aanpassen door te navigeren naar mijn computer>systeem eigenschappen>prestatie-instellingen> Geavanceerd> virtueel geheugen> gewijzigd.  

4. Configureer een SAP-cluster bron, de `SAP-SID-IP` test poort, met behulp van Power shell. Voer deze configuratie uit op een van de SAP ASCS/SCS-cluster knooppunten, zoals beschreven in [test poort configureren](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Een ASCS/SCS-exemplaar op het tweede ASCS/SCS-cluster knooppunt installeren

1. Installeer een SAP ASCS/SCS-exemplaar op het tweede cluster knooppunt. Start het SAP SWPM-installatie programma en navigeer naar **product**  >  **DBMS** > installatie > Application Server ABAP (of Java) > High-Availability systeem > ASCS/SCS-exemplaar > extra cluster knooppunt.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Een DBMS-exemplaar en SAP-toepassings servers installeren

Voltooi uw SAP-installatie door het volgende te installeren:

   * Een DBMS-exemplaar  
   * Een primaire SAP-toepassings server  
   * Een extra SAP-toepassings server  

## <a name="test-the-sap-ascsscs-instance-failover"></a>De ASCS/SCS-failover van het SAP-exemplaar testen 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Failover van cluster knooppunt A naar cluster knooppunt B en back
In dit test scenario verwijzen we naar cluster knooppunt sapascs1 als knoop punt A en naar cluster knooppunt sapascs2 als knoop punt B.

1. Controleer of de cluster bronnen worden uitgevoerd op knoop punt A. ![ Afbeelding 1: failover-cluster resources van Windows Server die worden uitgevoerd op knoop punt A vóór de failover-test](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Start cluster knooppunt A opnieuw. De SAP-cluster bronnen worden verplaatst naar cluster knooppunt B. ![ Afbeelding 2: Windows Server failover cluster-resources die worden uitgevoerd op knoop punt B na de failover-test](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Invoer test vergren delen

1. Controleer of de SAP-server voor in wachtrij plaatsen (ERS) actief is  
2. Meld u aan bij het SAP-systeem, voer de trans actie SU01 uit en open een gebruikers-ID in de modus wijzigen. Hiermee wordt een SAP-vergrendelings vermelding gegenereerd.  
3. Wanneer u bent aangemeld bij het SAP-systeem, geeft u het item vergren delen door te navigeren naar trans actie ST12.  
4. Failover van ASCS-resources van cluster knooppunt A naar cluster knooppunt B.  
5. Controleer of de vergrendelings vermelding is gegenereerd voordat de ASCS/SCS-failover van de SAP-cluster bronnen wordt behouden.  

![Afbeelding 3: vergrendelings vermelding blijft behouden na failover-test](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Zie voor meer informatie [problemen oplossen voor het in de wachtrij plaatsen van een failover in ASCS met ers](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Meer informatie over het instellen van hoge Beschik baarheid en het plannen van nood herstel van SAP 
* HANA op Azure (grote instanties), Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood geval op Azure](hana-overview-high-availability-disaster-recovery.md).
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
