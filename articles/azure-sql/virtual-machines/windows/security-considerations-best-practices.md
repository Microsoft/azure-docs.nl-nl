---
title: Beveiligings overwegingen | Microsoft Docs
description: Dit onderwerp bevat algemene richt lijnen voor het beveiligen van SQL Server die worden uitgevoerd op een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 54010359f226fe02336f039e3dcbb98075e9b06a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97360081"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit onderwerp bevat algemene beveiligings richtlijnen die u helpen bij het opzetten van beveiligde toegang tot SQL Server exemplaren in een virtuele Azure-machine (VM).

Azure voldoet aan verschillende industriële voor schriften en standaarden waarmee u een compatibele oplossing kunt bouwen met SQL Server uitgevoerd op een virtuele machine. Zie [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/)voor meer informatie over de naleving van de regelgeving met Azure.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Toegang tot de virtuele SQL-machine beheren

Wanneer u uw SQL Server virtuele machine maakt, moet u overwegen hoe u zorgvuldig kunt bepalen wie toegang heeft tot de machine en SQL Server. Over het algemeen moet u het volgende doen:

- Beperk de toegang tot SQL Server tot alleen de toepassingen en clients die er behoefte aan hebben.
- Volg de aanbevolen procedures voor het beheren van gebruikers accounts en wacht woorden.

In de volgende secties vindt u suggesties voor het door lopen van deze punten.

## <a name="secure-connections"></a>Beveiligde verbindingen

Wanneer u een SQL Server virtuele machine maakt met een galerie-afbeelding, biedt de **SQL Server connectiviteits** optie u de keuze van **lokale (binnen VM)**, **privé (binnen Virtual Network)** of **openbaar (Internet)**.

![SQL Server connectiviteit](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Voor de beste beveiliging kiest u de meest beperkende optie voor uw scenario. Als u bijvoorbeeld een toepassing uitvoert die toegang heeft tot SQL Server op dezelfde VM, is **lokaal** de veiligste keuze. Als u een Azure-toepassing uitvoert waarvoor toegang tot de SQL Server vereist is, wordt de communicatie met **privé** alleen SQL Server binnen het opgegeven [virtuele Azure-netwerk](../../../virtual-network/virtual-networks-overview.md). Als u **open bare** (Internet) toegang tot de SQL Server virtuele machine nodig hebt, moet u ervoor zorgen dat u andere aanbevolen procedures in dit onderwerp volgt om uw aanval Surface Area te verminderen.

De geselecteerde opties in de portal gebruiken binnenkomende beveiligings regels op de [netwerk beveiligings groep](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) van de VM (NSG) om netwerk verkeer naar uw virtuele machine toe te staan of te weigeren. U kunt nieuwe regels voor binnenkomende NSG wijzigen of maken om verkeer toe te staan voor de SQL Server poort (standaard 1433). U kunt ook specifieke IP-adressen opgeven die via deze poort mogen communiceren.

![Regels voor netwerkbeveiligingsgroepen](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Naast NSG-regels om netwerk verkeer te beperken, kunt u ook de Windows Firewall op de virtuele machine gebruiken.

Als u eind punten met het klassieke implementatie model gebruikt, verwijdert u alle eind punten op de virtuele machine als u deze niet gebruikt. Zie [de ACL voor een eind punt beheren](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)voor instructies over het gebruik van acl's met eind punten. Dit is niet nodig voor Vm's die gebruikmaken van de Azure Resource Manager.

Ten slotte kunt u versleutelde verbindingen inschakelen voor het exemplaar van de SQL Server data base-engine in uw virtuele Azure-machine. Configureer het SQL Server-exemplaar met een ondertekend certificaat. Zie [versleutelde verbindingen met de data base-engine](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) en de [syntaxis van de verbindings reeks](/dotnet/framework/data/adonet/connection-string-syntax)inschakelen voor meer informatie.

## <a name="encryption"></a>Versleuteling

Managed disks biedt Server-Side versleuteling en Azure Disk Encryption. [Versleuteling aan de server zijde](../../../virtual-machines/disk-encryption.md) biedt versleuteling-at-rest en beveiligt uw gegevens om te voldoen aan de verplichtingen van de beveiliging en naleving van uw organisatie. [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) gebruikt BitLocker-of DM-Crypt-technologie en integreert met Azure Key Vault om zowel het besturings systeem als de gegevens schijven te versleutelen. 

## <a name="use-a-non-default-port"></a>Een niet-standaard poort gebruiken

Standaard gebruikt SQL Server een bekende poort, namelijk 1433. Configureer SQL Server om te Luis teren naar een niet-standaard poort, zoals 1401, voor een betere beveiliging. Als u een installatie kopie van SQL Server galerie inricht in de Azure Portal, kunt u deze poort opgeven op de Blade **SQL Server instellingen** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Als u dit na de inrichting wilt configureren, hebt u twee opties:

- Voor virtuele machines van Resource Manager kunt u **beveiliging** selecteren op basis van de resource van de [virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Dit biedt een optie om de poort te wijzigen.

  ![TCP-poort wijziging in Portal](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Voor klassieke Vm's of voor SQL Server Vm's die niet zijn ingericht met de portal, kunt u de poort hand matig configureren door extern verbinding te maken met de virtuele machine. Zie [een server configureren om te Luis teren naar een specifieke TCP-poort](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)voor de configuratie stappen. Als u deze hand matige techniek gebruikt, moet u ook een Windows Firewall regel toevoegen om binnenkomend verkeer op die TCP-poort toe te staan.

> [!IMPORTANT]
> Het is een goed idee om een niet-standaard poort op te geven als uw SQL Server poort is geopend voor open bare Internet verbindingen.

Wanneer SQL Server op een niet-standaard poort luistert, moet u de poort opgeven wanneer u verbinding maakt. Denk bijvoorbeeld aan een scenario waarbij het IP-adres van de server 13.55.255.255 is en SQL Server luistert op poort 1401. Als u verbinding wilt maken met SQL Server, geeft u `13.55.255.255,1401` in de Connection String op.

## <a name="manage-accounts"></a>Accounts beheren

U wilt niet dat aanvallers eenvoudig account namen of wacht woorden raden. Gebruik de volgende tips om te helpen:

- Maak een uniek lokaal Administrator-account dat niet de naam **Administrator** heeft.

- Gebruik complexe sterke wacht woorden voor al uw accounts. Zie een artikel met [een sterk wacht woord maken](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) voor meer informatie over het maken van een sterk wacht woord.

- In azure wordt standaard Windows-verificatie geselecteerd tijdens de installatie van de virtuele machine SQL Server. Daarom is de **sa** -aanmelding uitgeschakeld en wordt er een wacht woord toegewezen door Setup. U wordt aangeraden de **sa** -aanmelding niet te gebruiken of in te scha kelen. Als u een SQL-aanmelding nodig hebt, gebruikt u een van de volgende strategieën:

  - Maak een SQL-account met een unieke naam die het lidmaatschap van de **sysadmin** heeft. U kunt dit doen vanuit de portal door **SQL-verificatie** in te scha kelen tijdens het inrichten.

    > [!TIP] 
    > Als u geen SQL-verificatie inschakelt tijdens het inrichten, moet u de verificatie modus hand matig wijzigen in **SQL Server en Windows-verificatie modus**. Zie voor meer informatie [Server verificatie modus wijzigen](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Als u de **sa** -aanmelding moet gebruiken, schakelt u de aanmelding na het inrichten in en wijst u een nieuw sterk wacht woord toe.

## <a name="additional-best-practices"></a>Aanvullende aanbevolen procedures

Naast de procedures die in dit onderwerp worden beschreven, raden we u aan om de aanbevolen procedures voor beveiliging te controleren en implementeren vanuit zowel traditionele on-premises beveiligings procedures als aanbevolen procedures voor de beveiliging van virtuele machines. 

Zie [beveiligings overwegingen voor een SQL Server-installatie](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) en het [Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)voor meer informatie over on-premises beveiligings procedures. 

Voor meer informatie over beveiliging van de virtuele machine raadpleegt u het [overzicht van beveiliging van virtuele machines](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor de prestaties van SQL Server op Azure virtual machines](performance-guidelines-best-practices.md)als u ook geïnteresseerd bent in Aanbevolen procedures voor de prestaties.

Zie [SQL Server op azure virtual machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's. Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).