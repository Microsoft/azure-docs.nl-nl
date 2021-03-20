---
title: VMware vCenter-servers beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u VMware vCenter kunt toevoegen en beheren voor nood herstel van virtuele VMware-machines in azure met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84692516"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server beheren

In dit artikel vindt u een overzicht van de beheer acties op een VMware vCenter Server in [Azure site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Vereisten voor vCenter Server controleren

De vereisten voor vCenter-servers en Vm's tijdens nood herstel van virtuele VMware-machines naar Azure worden weer gegeven in de [ondersteunings matrix](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Een account instellen voor automatische detectie

Wanneer u herstel na nood gevallen instelt voor on-premises VMware-Vm's, moet Site Recovery toegang hebben tot de vCenter Server/vSphere-host. De Site Recovery process server kan vervolgens Vm's automatisch detecteren en failover naar behoefte uitvoeren. De proces server wordt standaard uitgevoerd op de configuratie Server Site Recovery. Voeg als volgt een account toe voor de configuratie server om verbinding te maken met de vCenter Server/vSphere-host:

1. Meld u aan bij de configuratie server.
1. Open het hulp programma voor configuratie server (_cspsconfigtool.exe_) met behulp van de snelkoppeling op het bureau blad.
1. Klik op het tabblad **account beheren** op **account toevoegen**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Geef de account gegevens op en klik op **OK** om deze toe te voegen. Het account moet de bevoegdheden hebben in de tabel account machtigingen.

   > [!NOTE]
   > Het duurt ongeveer 15 minuten om account gegevens te synchroniseren met Site Recovery.

### <a name="account-permissions"></a>Accountmachtigingen

|**Taak** | **Account** | **Machtigingen** | **Details**|
|--- | --- | --- | ---|
|**Detectie/migratie van VM'S (zonder failback)** | Mini maal een alleen-lezen gebruikers account. | Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar onderliggend** item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).|
|**Replicatie/failover** | Mini maal een alleen-lezen gebruikers account. | Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar** onderliggend item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).<br/><br/> Nuttig voor migratie doeleinden, maar niet de volledige replicatie, failover, failback.|
|**Replicatie/failover/failback** | U wordt aangeraden een rol (AzureSiteRecoveryRole) met de vereiste machtigingen te maken en vervolgens de rol toe te wijzen aan een VMware-gebruiker of-groep. | Data Center-object-> door geven naar onderliggend object, Role = AzureSiteRecoveryRole<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar onderliggend** item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).|

## <a name="add-vmware-server-to-the-vault"></a>VMware-Server toevoegen aan de kluis

Wanneer u herstel na nood gevallen instelt voor on-premises VMware-Vm's, voegt u de vCenter Server-vSphere-host waarop u de virtuele machines op de Site Recovery kluis detecteert, als volgt toe:

1. Open de configuratie server in kluis > site Recovery configuratie servers van de **infra structuur**  >  .
1. Klik op de pagina **Details** op **vCenter**.
1. Geef in **VCenter toevoegen** een beschrijvende naam op voor de vSphere-host of vCenter-Server.
1. Geef het IP-adres of de FQDN van de server op.
1. Behoud poort 443 tenzij de VMware-servers zijn geconfigureerd om te luisteren naar aanvragen van een andere poort.
1. Selecteer het account dat wordt gebruikt om verbinding te maken met de VMware vCenter-of vSphere ESXi-server. Klik vervolgens op **OK**.

## <a name="modify-credentials"></a>Referenties wijzigen

Als dat nodig is, kunt u de referenties die worden gebruikt om verbinding te maken met de vCenter Server/vSphere-host als volgt wijzigen:

1. Meld u aan bij de configuratie server.
1. Open het hulp programma voor configuratie server (_cspsconfigtool.exe_) met behulp van de snelkoppeling op het bureau blad.
1. Klik op het tabblad **account beheren** op **account toevoegen** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Geef de nieuwe account gegevens op en klik op **OK**. Het account heeft de machtigingen nodig die worden vermeld in de tabel [account machtigingen](#account-permissions) .
1. Open de configuratie server in de kluis > site Recovery configuratie servers van de **infra structuur**  >  .
1. Klik in **Details** op **server vernieuwen**.
1. Nadat de taak server vernieuwen is voltooid, selecteert u de vCenter Server.
1. In **samen vatting** selecteert u het zojuist toegevoegde account in **VCenter server/vSphere host-account** en klikt u op **Opslaan**.

   ![wijzigen-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Een vCenter Server verwijderen

1. Open de configuratie server in de kluis > site Recovery configuratie servers van de **infra structuur**  >  .
1. Selecteer op de pagina **Details** de vCenter-Server.
1. Klik op de knop **verwijderen** .

   ![account verwijderen](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Wijzig het IP-adres en de poort

U kunt het IP-adres van de vCenter Server wijzigen of de poorten die worden gebruikt voor communicatie tussen de server en Site Recovery. Site Recovery heeft standaard toegang tot de hostgegevens vCenter Server/vSphere via poort 443.

1. Klik in de kluis > **site Recovery infrastructuur**  >  **configuratie servers** op de configuratie server waaraan de vCenter Server is toegevoegd.
1. Klik in **vCenter-servers** op de vCenter Server u wilt wijzigen.
1. Werk in **samen vatting** het IP-adres en de poort bij en sla de wijzigingen op.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Als u de wijzigingen van kracht wilt laten worden, wacht u 15 minuten of [vernieuwt u de configuratie server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Alle Vm's migreren naar een nieuwe server

Als u alle Vm's wilt migreren om een nieuwe vCenter Server te gebruiken, hoeft u alleen het IP-adres bij te werken dat aan de vCenter Server is toegewezen. Voeg nog geen VMware-account toe, omdat dit kan leiden tot dubbele vermeldingen. Werk het adres als volgt bij:

1. Klik in de kluis > **site Recovery infrastructuur**  >  **configuratie servers** op de configuratie server waaraan de vCenter Server is toegevoegd.
1. Klik in de sectie **vCenter-servers** op de vCenter Server waaruit u wilt migreren.
1. Werk in **samen vatting** het IP-adres bij naar dat van de nieuwe vCenter Server en sla de wijzigingen op.
1. Zodra het IP-adres is bijgewerkt, begint Site Recovery met het ontvangen van de detectie gegevens van de virtuele machine vanuit de nieuwe vCenter Server. Dit heeft geen invloed op lopende replicatie activiteiten.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Een aantal virtuele machines migreren naar een nieuwe server

Als u slechts enkele van uw gerepliceerde Vm's naar een nieuwe vCenter Server wilt migreren, gaat u als volgt te werk:

1. [Voeg](#add-vmware-server-to-the-vault) de nieuwe vCenter Server toe aan de configuratie server.
1. [Schakel replicatie uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor virtuele machines die worden verplaatst naar de nieuwe server.
1. Migreer de virtuele machines in VMware naar de nieuwe vCenter Server.
1. [Schakel de replicatie](vmware-azure-tutorial.md#enable-replication) voor de gemigreerde Vm's opnieuw in en selecteer de nieuwe vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>De meeste Vm's migreren naar een nieuwe server

Als het aantal Vm's dat u wilt migreren naar een nieuwe vCenter Server hoger is dan het aantal Vm's dat op de oorspronkelijke vCenter Server blijft, gaat u als volgt te werk:

1. [Werk het IP-adres](#modify-the-ip-address-and-port) dat is toegewezen aan de vCenter Server in de instellingen van de configuratie server, bij naar het adres van de nieuwe vCenter Server.
1. [Schakel replicatie uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor de enkele vm's die op de oude server achterblijven.
1. [Voeg de oude vCenter Server](#add-vmware-server-to-the-vault) en het bijbehorende IP-adres toe aan de configuratie server.
1. [Schakel replicatie opnieuw](vmware-azure-tutorial.md#enable-replication) in voor de vm's die op de oude server achterblijven.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen oplossen vCenter Server detectie fouten](vmware-azure-troubleshoot-vcenter-discovery-failures.md)als u problemen ondervindt.
