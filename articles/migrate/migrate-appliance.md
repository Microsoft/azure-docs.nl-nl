---
title: Azure Migrate-apparaat
description: Biedt een samenvatting van de ondersteuning voor Azure Migrate apparaat.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714664"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

Dit artikel bevat een overzicht van de vereisten en ondersteuningsvereisten voor Azure Migrate apparaat.

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Wordt gebruikt om**
--- | --- | ---
**Detectie en evaluatie van servers die worden uitgevoerd in een VMware-omgeving** | Azure Migrate: Detectie en evaluatie | Servers ontdekken die worden uitgevoerd in uw VMware-omgeving<br/><br/> Detectie van geïnstalleerde software-inventaris, afhankelijkheidsanalyse zonder agent en SQL Server exemplaren en databases.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.
**Migratie zonder agent van servers die worden uitgevoerd in een VMware-omgeving** | Azure Migrate:Servermigratie | Servers ontdekken die worden uitgevoerd in uw VMware-omgeving. <br/><br/> Repliceer servers zonder agents op deze servers te installeren.
**Detectie en evaluatie van servers die worden uitgevoerd in de Hyper-V-omgeving** | Azure Migrate: Detectie en evaluatie | Servers ontdekken die worden uitgevoerd in uw Hyper-V-omgeving.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.
**Detectie en evaluatie van fysieke of gevirtualiseerde servers on-premises** |  Azure Migrate: Detectie en evaluatie |  Fysieke of gevirtualiseerde servers on-premises ontdekken.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.

## <a name="deployment-methods"></a>Implementatiemethoden

Het apparaat kan op een aantal manieren worden geïmplementeerd:

- Het apparaat kan worden geïmplementeerd met behulp van een sjabloon voor servers die worden uitgevoerd in VMware- of Hyper-V-omgeving[(OVA-sjabloon voor VMware](how-to-set-up-appliance-vmware.md) of [VHD voor Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat implementeren voor de VMware- of Hyper-V-omgeving met behulp van een [PowerShell-installatiescript.](deploy-appliance-script.md)
- In Azure Government moet u het apparaat implementeren met behulp van een PowerShell-installatiescript. Raadpleeg hier de [implementatiestappen.](deploy-appliance-script-government.md)
- Voor fysieke of gevirtualiseerde servers on-premises of een andere cloud implementeert u het apparaat altijd met behulp van een PowerShell-installatiescript. Raadpleeg hier de [implementatiestappen.](how-to-set-up-appliance-physical.md)
- Downloadkoppelingen zijn beschikbaar in de onderstaande tabellen.

## <a name="appliance---vmware"></a>Apparaat - VMware

De volgende tabel bevat een overzicht van Azure Migrate vereisten voor VMware.

> [!Note]
> Detectie en evaluatie van SQL Server en databases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

**Vereiste** | **VMware**
--- | ---
**Machtigingen** | Als u lokaal of extern toegang wilt krijgen tot configuration manager van het apparaat, moet u een lokaal of domeingebruikersaccount met beheerdersbevoegdheden hebben op de apparaatserver.
**Apparaatservices** | Het apparaat heeft de volgende services:<br/><br/> - **Apparaatconfiguratiebeheer:** dit is een webtoepassing die kan worden geconfigureerd met brongegevens om de detectie en evaluatie van servers te starten.<br/> - **VMware-detectieagent:** de agent verzamelt metagegevens van de serverconfiguratie die kunnen worden gebruikt om als on-premises evaluaties te maken.<br/>- **VMware-evaluatieagent:** de agent verzamelt metagegevens van serverprestaties die kunnen worden gebruikt om evaluaties op basis van prestaties te maken.<br/>- **Service voor automatisch bijwerken:** de service zorgt ervoor dat alle agents op het apparaat up-to-date blijven. Deze wordt automatisch elke 24 uur uitgevoerd.<br/>- **DRA-agent:** coördineert serverreplicatie en coördineert de communicatie tussen gerepliceerde servers en Azure. Wordt alleen gebruikt bij het repliceren van servers naar Azure met behulp van migratie zonder agent.<br/>- **Gateway:** hiermee worden gerepliceerde gegevens naar Azure verzendt. Wordt alleen gebruikt bij het repliceren van servers naar Azure met behulp van migratie zonder agent.<br/>- **SQL-agent voor detectie en evaluatie:** verzendt de configuratie- en prestatiemetagegevens van SQL Server exemplaren en databases naar Azure.
**Projectlimieten** |  Een apparaat kan slechts bij één project worden geregistreerd.<br/> Eén project kan meerdere geregistreerde apparaten hebben.
**Detectielimieten** | Een apparaat kan maximaal 10.000 servers op een vCenter Server.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**Ondersteunde implementatie** | Implementeer als nieuwe server die wordt uitgevoerd op vCenter Server met behulp van een OVA-sjabloon.<br/><br/> Implementeer op een bestaande server met Windows Server 2016 met behulp van het PowerShell-installatiescript.
**OVA-sjabloon** | Downloaden van project of [van hier](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> De downloadgrootte is 11,9 GB.<br/><br/> De gedownloade apparaatsjabloon wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is.<br/>Als de evaluatieperiode bijna is verlopen, wordt u aangeraden een nieuw apparaat te downloaden en te implementeren met behulp van een OVA-sjabloon of om de besturingssysteemlicentie van de apparaatserver te activeren.
**OVA-verificatie** | [Controleer](tutorial-discover-vmware.md#verify-security) de OVA-sjabloon die is gedownload uit het project door de hash-waarden te controleren.
**PowerShell-script** | Raadpleeg dit artikel [over het](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) implementeren van een apparaat met behulp van het PowerShell-installatiescript.<br/><br/> 
**Hardware- en netwerkvereisten** |  Het apparaat moet worden uitgevoerd op een server met Windows Server 2016, 32 GB RAM, 8 vCCPUs, ongeveer 80 GB aan schijfopslag en een externe virtuele switch.<br/> Het apparaat vereist internettoegang, rechtstreeks of via een proxy.<br/><br/> Als u het apparaat implementeert met behulp van een OVA-sjabloon, hebt u voldoende resources op de vCenter Server om een server te maken die voldoet aan de hardwarevereisten.<br/><br/> Als u het apparaat op een bestaande server hebt uitgevoerd, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_
**VMware-vereisten** | Als u het apparaat als een server op vCenter Server implementeert, moet het worden geïmplementeerd op een vCenter Server met versie 5.5, 6.0, 6.5 of 6.7 en een ESXi-host met versie 5.5 of hoger.<br/><br/> 
**VDDK (migratie zonder agent)** | Als u het apparaat wilt gebruiken voor migratie zonder agent van servers, moet VMware vSphere VDDK op de apparaatserver zijn geïnstalleerd.

## <a name="appliance---hyper-v"></a>Apparaat - Hyper-V

**Vereiste** | **Hyper-V**
--- | ---
**Machtigingen** | Als u lokaal of extern toegang wilt krijgen tot configuration manager van het apparaat, moet u een lokaal of domeingebruikersaccount met beheerdersbevoegdheden hebben op de apparaatserver.
**Apparaatservices** | Het apparaat heeft de volgende services:<br/><br/> - **Apparaatconfiguratiebeheer:** dit is een webtoepassing die kan worden geconfigureerd met brongegevens om de detectie en evaluatie van servers te starten.<br/> - **Detectieagent:** de agent verzamelt metagegevens van de serverconfiguratie die kunnen worden gebruikt om te maken als on-premises evaluaties.<br/>- **Evaluatieagent:** de agent verzamelt metagegevens van serverprestaties die kunnen worden gebruikt voor het maken van evaluaties op basis van prestaties.<br/>- **Service voor automatisch bijwerken:** de service zorgt ervoor dat alle agents op het apparaat up-to-date blijven. Deze wordt automatisch elke 24 uur uitgevoerd.
**Projectlimieten** |  Een apparaat kan slechts bij één project worden geregistreerd.<br/> Eén project kan meerdere geregistreerde apparaten hebben.
**Detectielimieten** | Een apparaat kan maximaal 5000 servers ontdekken die worden uitgevoerd in een Hyper-V-omgeving.<br/> Een apparaat kan verbinding maken met maximaal 300 Hyper-V-hosts.
**Ondersteunde implementatie** | Implementeer als server die wordt uitgevoerd op een Hyper-V-host met behulp van een VHD-sjabloon.<br/><br/> Implementeer op een bestaande server met Windows Server 2016 met behulp van het PowerShell-installatiescript.
**VHD-sjabloon** | Zip-bestand met een VHD. Download vanaf project of [hier](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> De downloadgrootte is 8,91 GB.<br/><br/> De gedownloade apparaatsjabloon wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is.<br/> Als de evaluatieperiode bijna is verlopen, wordt u aangeraden een nieuw apparaat te downloaden en te implementeren, of de besturingssysteemlicentie van de apparaatserver te activeren.
**VHD-verificatie** | [Controleer](tutorial-discover-hyper-v.md#verify-security) de VHD-sjabloon die u van het project hebt gedownload door de hash-waarden te controleren.
**PowerShell-script** | Raadpleeg dit artikel [over het](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) implementeren van een apparaat met behulp van het PowerShell-installatiescript.<br/>
**Hardware- en netwerkvereisten**  |  Het apparaat moet worden uitgevoerd op een server met Windows Server 2016, 16 GB RAM, 8 vCCPUs, ongeveer 80 GB aan schijfopslag en een externe virtuele switch.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en internettoegang, rechtstreeks of via een proxy.<br/><br/> Als u het apparaat als een server op een Hyper-V-host hebt uitgevoerd, hebt u voldoende resources op de host nodig om een server te maken die voldoet aan de hardwarevereisten.<br/><br/> Als u het apparaat op een bestaande server hebt uitgevoerd, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_
**Vereisten voor Hyper-V** | Als u het apparaat implementeert met de VHD-sjabloon, is het apparaat dat wordt geleverd door Azure Migrate Hyper-V VM versie 5.0.<br/><br/> Op de Hyper-V-host moet Windows Server 2012 R2 of hoger worden uitgevoerd.

## <a name="appliance---physical"></a>Apparaat - fysiek

**Vereiste** | **Fysiek**
--- | ---
**Machtigingen** | Als u lokaal of extern toegang wilt krijgen tot configuration manager van het apparaat, moet u een lokaal of domeingebruikersaccount met beheerdersbevoegdheden hebben op de apparaatserver.
**Apparaatservices** | Het apparaat heeft de volgende services:<br/><br/> - **Apparaatconfiguratiebeheer:** dit is een webtoepassing die kan worden geconfigureerd met brongegevens om de detectie en evaluatie van servers te starten.<br/> - **Detectieagent:** de agent verzamelt metagegevens van de serverconfiguratie die kunnen worden gebruikt om te maken als on-premises evaluaties.<br/>- **Evaluatieagent:** de agent verzamelt metagegevens van serverprestaties die kunnen worden gebruikt om evaluaties op basis van prestaties te maken.<br/>- **Service voor automatisch bijwerken:** de service zorgt ervoor dat alle agents op het apparaat up-to-date blijven. Deze wordt automatisch elke 24 uur uitgevoerd.
**Projectlimieten** |  Een apparaat kan slechts bij één project worden geregistreerd.<br/> Eén project kan meerdere geregistreerde apparaten hebben.<br/>
**Detectielimieten** | Een apparaat kan maximaal 1000 fysieke servers ontdekken.
**Ondersteunde implementatie** | Implementeer op een bestaande server met Windows Server 2016 met behulp van het PowerShell-installatiescript.
**PowerShell-script** | Download het script (AzureMigrateInstaller.ps1) in een zip-bestand van het project of van [hier .](https://go.microsoft.com/fwlink/?linkid=2140334) [Meer informatie](tutorial-discover-physical.md).<br/><br/> De downloadgrootte is 85,8 MB.
**Scriptverificatie** | [Controleer het](tutorial-discover-physical.md#verify-security) PowerShell-installatiescript dat u van het project hebt gedownload door de hash-waarden te controleren.
**Hardware- en netwerkvereisten** |  Het apparaat moet worden uitgevoerd op een server met Windows Server 2016, 16 GB RAM, 8 vCCPUs, ongeveer 80 GB aan schijfopslag.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en internettoegang, rechtstreeks of via een proxy.<br/><br/> Als u het apparaat op een bestaande server hebt uitgevoerd, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_

## <a name="url-access"></a>URL-toegang

Het Azure Migrate apparaat moet zijn verbonden met internet.

- Wanneer u het apparaat implementeert, Azure Migrate een connectiviteitscontrole naar de vereiste URL's.
- U moet toegang toestaan tot alle URL's in de lijst. Als u alleen evaluaties doet, kunt u de URL's overslaan die zijn gemarkeerd als vereist voor migratie zonder VMware-agent.
- Als u een OP URL gebaseerde proxy gebruikt om verbinding te maken met internet, moet u ervoor zorgen dat de proxy alle CNAME-records die zijn ontvangen tijdens het opzoeken van de URL's, omkeert.

### <a name="public-cloud-urls"></a>URL's van openbare cloud

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigeer naar de Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak Azure Active Directory(AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.azure.com | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate.
*.services.visualstudio.com | Apparaatlogboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.<br/> Opmerking: zorg ervoor dat servers die moeten worden gerepliceerd toegang hebben tot deze gegevens.
aka.ms/* | Toegang tot koppelingen toestaan; wordt gebruikt voor het downloaden en installeren van de nieuwste updates voor apparaatservices.
download.microsoft.com/download | Downloads vanuit het Microsoft Downloadcentrum toestaan.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Verbinding maken met Azure Migrate service-URL's.
*.hypervrecoverymanager.windowsazure.com | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Verbinding maken met Azure Migrate service-URL's.
*.blob.core.windows.net |  **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/>Gegevens uploaden naar opslag voor migratie.

### <a name="government-cloud-urls"></a>CLOUD-URL's van de overheid

**URL** | **Details**  
--- | --- |
*.portal.azure.us  | Navigeer naar de Azure Portal.
graph.windows.net | Meld u aan bij uw Azure-abonnement.
login.microsoftonline.us  | Maak Azure Active Directory(AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.usgovcloudapi.net | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate service.
*.services.visualstudio.com | Upload apparaatlogboeken die worden gebruikt voor interne bewaking.
*.vault.usgovcloudapi.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Toegang tot aka-koppelingen toestaan; wordt gebruikt voor het downloaden en installeren van de nieuwste updates voor apparaatservices.
download.microsoft.com/download | Hiermee staat u downloads van het Microsoft Downloadcentrum toe.
*.servicebus.usgovcloudapi.net  | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Verbinding maken met Azure Migrate-service-URL's.
*.hypervrecoverymanager.windowsazure.us | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Verbinding maken met Azure Migrate-service-URL's.
*.blob.core.usgovcloudapi.net  |  **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/>Gegevens uploaden naar opslag voor migratie.
*.applicationinsights.us | Upload apparaatlogboeken die worden gebruikt voor interne bewaking.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>URL's van de openbare cloud voor private link-connectiviteit

Het apparaat moet toegang hebben tot de volgende URL's (rechtstreeks of via proxy) via en boven private link-toegang. 

**URL** | **Details**  
--- | --- | 
*.portal.azure.com  | Navigeer naar de Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak Azure Active Directory(AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.azure.com | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate.
*.services.visualstudio.com (optioneel) | Apparaatlogboeken uploaden die worden gebruikt voor interne bewaking.
aka.ms/* (optioneel) | Toegang tot koppelingen toestaan; wordt gebruikt voor het downloaden en installeren van de nieuwste updates voor apparaatservices.
download.microsoft.com/download | Downloads vanuit het Microsoft Downloadcentrum toestaan.
*.servicebus.windows.net | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Communicatie tussen het apparaat en de Azure Migrate service.
*.vault.azure.net | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/>  Zorg ervoor dat servers die moeten worden gerepliceerd toegang hebben tot deze gegevens.
*.hypervrecoverymanager.windowsazure.com | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Verbinding maken met Azure Migrate service-URL's.
*.blob.core.windows.net |  **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/>Gegevens uploaden naar opslag voor migratie.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>GOVERNMENT-cloud-URL's voor private link-connectiviteit   

Het apparaat moet toegang hebben tot de volgende URL's (rechtstreeks of via proxy) via en boven private link-toegang. 

**URL** | **Details**  
--- | --- |
*.portal.azure.us  | Navigeer naar de Azure Portal.
graph.windows.net | Meld u aan bij uw Azure-abonnement.
login.microsoftonline.us  | Maak Azure Active Directory(AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.usgovcloudapi.net | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate service.
*.services.visualstudio.com (optioneel) | Upload apparaatlogboeken die worden gebruikt voor interne bewaking.
aka.ms/* (optioneel) | Toegang tot aka-koppelingen toestaan; wordt gebruikt voor het downloaden en installeren van de nieuwste updates voor apparaatservices.
download.microsoft.com/download | Hiermee staat u downloads van het Microsoft Downloadcentrum toe.
*.servicebus.usgovcloudapi.net  | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Communicatie tussen het apparaat en de Azure Migrate service. 
*.vault.usgovcloudapi.net | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Geheimen beheren in de Azure Key Vault.
*.hypervrecoverymanager.windowsazure.us | **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/> Verbinding maken met Azure Migrate service-URL's.
*.blob.core.usgovcloudapi.net  |  **Wordt gebruikt voor migratie zonder VMware-agent**<br/><br/>Gegevens uploaden naar opslag voor migratie.
*.applicationinsights.us (optioneel) | Upload de apparaatlogboeken die worden gebruikt voor interne bewaking.  

## <a name="collected-data---vmware"></a>Verzamelde gegevens - VMware

Het apparaat verzamelt configuratiemetagegevens, prestatiemetagegevens en serverafhankelijkheden (als afhankelijkheidsanalyse zonder agent [wordt](concepts-dependency-visualization.md) gebruikt).

### <a name="metadata"></a>Metagegevens

Metagegevens die door het Azure Migrate-apparaat worden ontdekt, helpen u te bepalen of servers gereed zijn voor migratie naar Azure, servers van de juiste grootte, kosten plannen en toepassingsafhankelijkheden analyseren. Microsoft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier is de volledige lijst met servermetagegevens die het apparaat verzamelt en naar Azure verzendt.

**Gegevens** | **Teller**
--- | ---
**Servergegevens** |
Server-id | vm.Config. InstanceUuid
Servernaam | vm.Config. Naam
vCenter Server-id | VMwareClient.Instance.Uuid
Beschrijving van de server | vm.Summary.Config. Aantekening
Productnaam licentie | vm.Client.ServiceContent.About.LicenseProductName
Besturingssysteemtype | vm.SummaryConfig.GuestFullName
Opstarttype | vm.Config. Firmware
Aantal kerngeheugens | vm.Config. Hardware.NumCPU
Geheugen (MB) | vm.Config. Hardware.MemoryMB
Aantal schijven | vm.Config. Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Lijst met schijfgrootten | vm.Config. Hardware.Device.ToList(). FindAll(x => is VirtualDisk)
Lijst met netwerkadapters | vm.Config. Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
CPU-gebruik | cpu.usage.average
Geheugengebruik |mem.usage.average
**Details per schijf** |
Waarde van schijfsleutel | Schijf. Sleutel
Aantal werkeenheden | Schijf. UnitNumber
Waarde van schijfcontrollersleutel | Schijf. ControllerKey.Value
Gigabytes ingericht | virtualDisk.DeviceInfo.Summary
Schijfnaam | Waarde gegenereerd met behulp van schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey.V Hebte
Leesbewerkingen per seconde | virtualDisk.numberReadAveraged.average
Schrijfbewerkingen per seconde | virtualDisk.numberWriteAveraged.average
Leesdoorvoer (MB per seconde) | virtualDisk.read.average
Schrijfdoorvoer (MB per seconde) | virtualDisk.write.average
**Details per NIC** |
Naam van netwerkadapter | Nic. Sleutel
MAC-adres | ((VirtualEthernetCard)nic). MacAddress
IPv4-adressen | vm.Guest.Net
IPv6-adressen | vm.Guest.Net
Leesdoorvoer (MB per seconde) | net.received.average
Schrijfdoorvoer (MB per seconde) | net.transmitted.average
**Details inventarispad** |
Name | Container. GetType(). Naam
Type onderliggend object | Container. ChildType
Naslaginformatie | Container. MoRef
Bovenliggende details | Container.Parent
Mapdetails per server | ((Map)container). ChildEntity.Type
Datacenterdetails per server | ((Datacenter)container). VmFolder
Datacenterdetails per hostmap | ((Datacenter)container). HostFolder
Clusterdetails per host | ((ClusterComputeResource)container). Host
Hostdetails per server | ((HostSystem)container). Vm

### <a name="performance-data"></a>Prestatiegegevens

Dit zijn de prestatiegegevens die een apparaat verzamelt voor een server die wordt uitgevoerd op VMware en naar Azure verzendt.

**Gegevens** | **Prestatiemeteritem** | **Impact van evaluatie**
--- | --- | ---
CPU-gebruik | cpu.usage.average | Aanbevolen servergrootte/-kosten
Geheugengebruik | mem.usage.average | Aanbevolen servergrootte/-kosten
Leesdoorvoer van schijf (MB per seconde) | virtualDisk.read.average | Berekening voor schijfgrootte, opslagkosten, servergrootte
Schrijfdoorvoer van schijf (MB per seconde) | virtualDisk.write.average | Berekening voor schijfgrootte, opslagkosten, servergrootte
Leesbewerkingen per seconde op schijf | virtualDisk.numberReadAveraged.average | Berekening voor schijfgrootte, opslagkosten, servergrootte
Schijfbewerkingen voor schrijfbewerkingen per seconde | virtualDisk.numberWriteAveraged.average  | Berekening voor schijfgrootte, opslagkosten, servergrootte
NIC-leesdoorvoer (MB per seconde) | net.received.average | Berekening voor servergrootte
NIC-schrijfdoorvoer (MB per seconde) | net.transmitted.average  |Berekening voor servergrootte

### <a name="installed-software-inventory"></a>Geïnstalleerde software-inventaris

Het apparaat verzamelt gegevens over geïnstalleerde software-inventaris op servers.

#### <a name="windows-server-software-inventory-data"></a>Inventarisgegevens van Windows Server-software

Dit zijn de software-inventarisgegevens die het apparaat verzamelt van elke Windows-server die in uw VMware-omgeving wordt ontdekt.

**Gegevens** | **Registerlocatie** | **Sleutel**
--- | --- | ---
Naam van de toepassing  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versie  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Provider  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Gegevens van Windows-serverfuncties

Dit zijn de functies die het apparaat verzamelt van elke Windows-server die wordt ontdekt in uw VMware-omgeving.

**Gegevens**  | **PowerShell-cmdlet** | **Eigenschap**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Functietype | Get-WindowsFeature  | FeatureType
Bovenliggend  | Get-WindowsFeature  | Bovenliggend

#### <a name="sql-server-metadata"></a>SQL Server metagegevens

Dit zijn de SQL Server die het apparaat verzamelt van elke Windows-server die wordt ontdekt in uw VMware-omgeving.

**Gegevens**  | **Registerlocatie**  | **Sleutel**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Editie  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Editie
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Sp
Versie  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Versie

#### <a name="windows-server-operating-system-data"></a>Besturingssysteemgegevens van Windows-server

Hier zijn de besturingssysteemgegevens die het apparaat verzamelt van elke Windows-server die in uw VMware-omgeving wordt ontdekt.

**Gegevens**  | **WMI-klasse**  | **WMI-klasse-eigenschap**
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Versie  | Win32_operatingsystem  | Versie
Architectuur  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Inventarisgegevens van Linux-serversoftware

Dit zijn de software-inventarisgegevens die het apparaat verzamelt van elke Linux-server die in uw VMware-omgeving wordt ontdekt. Op basis van het besturingssysteem van de server worden een of meer opdrachten uitgevoerd.

**Gegevens**  | **Opdrachten**
--- | ---
Name | rpm, dpkg-query, snap
Versie | rpm, dpkg-query, snap
Provider | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Besturingssysteemgegevens linux-server

Dit zijn de besturingssysteemgegevens die het apparaat verzamelt van elke Linux-server die in uw VMware-omgeving wordt ontdekt.

**Gegevens**  | **Opdrachten**
--- | ---
Name <br/> versie | Verzameld uit een of meer van de volgende bestanden:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architectuur | Uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server instanties en databases beheren

Het apparaat verzamelt gegevens over SQL Server instanties en databases.

> [!Note]
> Detectie en evaluatie van SQL Server en databases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

#### <a name="sql-database-metadata"></a>SQL database metagegevens

**Databasemetagegevens** | **Weergaven/SQL Server eigenschappen**
--- | ---
Unieke id van de database | sys.databases
Door de server gedefinieerde database-id | sys.databases
Naam van de database | sys.databases
Compatibiliteitsniveau van database | sys.databases
De naam van de database sorteren | sys.databases
Status van de database | sys.databases
Grootte van de database (in DATABASES) | sys.master_files
Station letter van locatie met gegevensbestanden | SERVERPROPERTY en Software\Microsoft\MSSQLServer\MSSQLServer
Lijst met databasebestanden | sys.databases, sys.master_files
Service Broker is al dan niet ingeschakeld | sys.databases
Database is ingeschakeld voor change data capture of niet | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metagegevens

**Servermetagegevens** | **Weergaven/ SQL Server-eigenschappen**
--- | ---
Servernaam |SERVERPROPERTY
FQDN | Verbindingsreeks afgeleid van detectie van geïnstalleerde toepassingen
Installatie-id | sys.dm_server_registry
Serverversie | SERVERPROPERTY
Servereditie | SERVERPROPERTY
Serverhostplatform (Windows/Linux) | SERVERPROPERTY
Productniveau van de server (RTM SP CTP) | SERVERPROPERTY
Standaardpad voor back-up | SERVERPROPERTY
Standaardpad van de gegevensbestanden | SERVERPROPERTY en Software\Microsoft\MSSQLServer\MSSQLServer
Standaardpad van de logboekbestanden | SERVERPROPERTY en Software\Microsoft\MSSQLServer\MSSQLServer
Nee. kernen op de server | sys.dm_os_schedulers, sys.dm_os_sys_info
Naam van serverseratie | SERVERPROPERTY
Nee. van kernen op de server met de status VISIBLE ONLINE | sys.dm_os_schedulers
Unieke server-id | sys.dm_server_registry
Ha is ingeschakeld of niet | SERVERPROPERTY
Buffergroepextensie al dan niet ingeschakeld | sys.dm_os_buffer_pool_extension_configuration
Failovercluster geconfigureerd of niet | SERVERPROPERTY
Server die alleen de Windows-verificatiemodus gebruikt | SERVERPROPERTY
Server installeert PolyBase | SERVERPROPERTY
Nee. van logische CPU's op het systeem | sys.dm_server_registry, sys.dm_os_sys_info
Verhouding van het aantal logische of fysieke kernen dat wordt blootgesteld door één fysiek processorpakket | sys.dm_os_schedulers, sys.dm_os_sys_info
Geen fysieke CPU's op het systeem | sys.dm_os_schedulers, sys.dm_os_sys_info
Datum- en tijdserver laatst gestart | sys.dm_server_registry
Maximaal servergeheugengebruik (in GB's) | sys.dm_os_process_memory
Totaal niet. van gebruikers in alle databases | sys.databases, sys.logins
Totale grootte van alle gebruikersdatabases | sys.databases
Grootte van tijdelijke database | sys.master_files, sys.config, sys.dm_os_sys_info
Nee. van aanmeldingen | sys.logins
Lijst met gekoppelde servers | sys.servers
Lijst met agent-taak | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metagegevens van prestaties

**Prestaties** | **Weergaven/ SQL Server-eigenschappen** | **Impact van evaluatie**
--- | --- | ---
SQL Server CPU-gebruik| sys.dm_os_ring_buffers| Aanbevolen SKU-grootte (CPU-dimensie)
Aantal logische SQL-CPU's| sys.dm_os_sys_info| Aanbevolen SKU-grootte (CPU-dimensie)
Fysiek SQL-geheugen in gebruik| sys.dm_os_process_memory| Ongebruikte
Percentage SQL-geheugengebruik| sys.dm_os_process_memory | Ongebruikte
CPU-gebruik van database| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Aanbevolen SKU-grootte (CPU-dimensie)
Databasegeheugen in gebruik (buffergroep)| sys.dm_os_buffer_descriptors| Aanbevolen SKU-grootte (geheugendimensie)
IO voor lezen/schrijven van bestanden| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (IO-dimensie)
File num of reads/writes| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (doorvoerdimensie)
Lezen/schrijven van IO-bestands-IO vastgelopen (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (dimensie IO-latentie)
Bestandsgrootte| sys.master_files| Aanbevolen SKU-grootte (opslagdimensie)


### <a name="application-dependency-data"></a>Afhankelijkheidsgegevens van toepassingen

Met afhankelijkheidsanalyse zonder agent worden de verbindings- en procesgegevens verzameld.

#### <a name="windows-server-dependencies-data"></a>Afhankelijkheden van Windows-servers

Dit zijn de verbindingsgegevens die het apparaat verzamelt van elke Windows-server, ingeschakeld voor afhankelijkheidsanalyse zonder agent.

**Gegevens** | **Opdrachten**
--- | ---
Lokale poort | Netstat
Lokaal IP-adres | Netstat
Externe poort | Netstat
Extern IP-adres | Netstat
TCP-verbindingstoestand | Netstat
Proces-id | Netstat
Aantal actieve verbindingen | Netstat

Dit zijn de verbindingsgegevens die het apparaat verzamelt van elke Windows-server, ingeschakeld voor afhankelijkheidsanalyse zonder agent.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
Procesnaam | Win32_Process | ExecutablePath
Procesargumenten | Win32_Process | Commandline
De naam van de toepassing | Win32_Process | Parameter VersionInfo.ProductName van de eigenschap ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Afhankelijkheden van Linux-servergegevens

Dit zijn de verbindingsgegevens die het apparaat verzamelt van elke Linux-server, ingeschakeld voor afhankelijkheidsanalyse zonder agent.

**Gegevens** | **Opdrachten**
--- | ---
Lokale poort | Netstat
Lokaal IP-adres | Netstat
Externe poort | Netstat
Extern IP-adres | Netstat
TCP-verbindingstoestand | Netstat
Aantal actieve verbindingen | Netstat
Proces-id  | Netstat
Procesnaam | ps
Procesargumenten | ps
De naam van de toepassing | dpkg of rpm

## <a name="collected-data---hyper-v"></a>Verzamelde gegevens - Hyper-V

Het apparaat verzamelt configuratie- en prestatiemetagegevens van servers die worden uitgevoerd in de Hyper-V-omgeving.

### <a name="metadata"></a>Metagegevens
Metagegevens die door het Azure Migrate apparaat worden ontdekt, helpen u te bepalen of servers gereed zijn voor migratie naar Azure, servers met de juiste grootte en kosten voor plannen. Microsoft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier is de volledige lijst met servermetagegevens die het apparaat verzamelt en naar Azure verzendt.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
**Servergegevens** | 
Serienummer van BIOS | Msvm_BIOSElement | BIOSSerialNumber
Servertype (Gen 1 of 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Weergavenaam van server | Msvm_VirtualSystemSettingData | ElementName
Serverversie | Msvm_ProcessorSettingData | VirtualQuantity
Geheugen (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximaal geheugen dat kan worden gebruikt door de server | Msvm_MemorySettingData | Limiet
Dynamisch geheugen ingeschakeld | Msvm_MemorySettingData | DynamicMemoryEnabled
Naam/versie/FQDN van besturingssysteem | Msvm_KvpExchangeComponent | Naamgegevens guestIntrinsicExchangeItems
Energiestatus van de server | Msvm_ComputerSystem | EnabledState
**Details per schijf** |
Schijf-id | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Type virtuele harde schijf | Msvm_VirtualHardDiskSettingData | Type
Grootte van virtuele harde schijf | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Bovenliggende virtuele harde schijf | Msvm_VirtualHardDiskSettingData | ParentPath
**Details per NIC** |
IP-adressen (synthetische NIC's) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ingeschakeld (synthetische NIC's) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-id (synthetische NIC's) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC-adres (synthetische NIC's) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC-id (verouderde NIC's) | MsvmEmulatedEthernetPortSetting-gegevens | InstanceID
MAC-id van NIC (verouderde NIC's) | MsvmEmulatedEthernetPortSetting Data | Adres

### <a name="performance-data"></a>Prestatiegegevens

Dit zijn de prestatiegegevens van de server die het apparaat verzamelt en naar Azure verzendt.

**Prestatiemeterklasse** | **Prestatiemeteritem** | **Impact van evaluatie**
--- | --- | ---
Hyper-V Hypervisor Virtuele processor | % run time gast | Aanbevolen servergrootte/-kosten
Hyper-V dynamisch geheugen Server | Huidige druk (%)<br/> Zichtbaar fysiek gastgeheugen (MB) | Aanbevolen servergrootte/-kosten
Hyper-V Virtual Storage-apparaat | Bytes per seconde lezen | Berekening voor schijfgrootte, opslagkosten, servergrootte
Hyper-V Virtual Storage-apparaat | Bytes per seconde schrijven | Berekening voor schijfgrootte, opslagkosten, servergrootte
Hyper-V-Virtual Network adapter | Ontvangen bytes per seconde | Berekening voor servergrootte
Hyper-V-Virtual Network adapter | Verzonden bytes per seconde | Berekening voor servergrootte

- CPU-gebruik is de som van al het gebruik voor alle virtuele processors die aan een server zijn gekoppeld.
- Geheugengebruik is (huidige druk * zichtbaar fysiek geheugen van gast) / 100.
- Waarden voor schijf- en netwerkgebruik worden verzameld uit de vermelde Hyper-V-prestatiemeters.

## <a name="collected-data---physical"></a>Verzamelde gegevens - Fysiek

Het apparaat verzamelt configuratie- en prestatiemetagegevens van fysieke of virtuele servers die on-premises worden uitgevoerd.

### <a name="metadata"></a>Metagegevens

Metagegevens die door het Azure Migrate apparaat worden ontdekt, helpen u te bepalen of servers gereed zijn voor migratie naar Azure, servers met de juiste grootte en kosten voor plannen. Microsoft gebruikt deze gegevens niet in een controle op de naleving van licenties.

### <a name="windows-server-metadata"></a>Metagegevens van Windows-server

Hier is de volledige lijst met windows-servermetagegevens die het apparaat verzamelt en naar Azure verzendt.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domein, Naam, PartOfDomain
Aantal processorkernen | Win32_PRocessor | NumberOfCores
Toegewezen geheugen | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-serienummer | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
Opstarttype | Win32_DiskPartition | Controleer op partitie met Type = **GPT:System** voor EFI/BIOS
Naam van besturingssysteem | Win32_OperatingSystem | Caption
Besturingssysteemversie |Win32_OperatingSystem | Versie
Architectuur van besturingssysteem | Win32_OperatingSystem | OSArchitecture
Aantal schijven | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Schijfgrootte | Win32_DiskDrive | Grootte
Lijst met NIC's | Win32_NetworkAdapterConfiguration | Beschrijving, Index
IP-adres van NIC | Win32_NetworkAdapterConfiguration | IPAddress
MAC-adres van NIC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Metagegevens van Linux-server

Hier is de volledige lijst met linux-servermetagegevens die het apparaat verzamelt en naar Azure verzendt.

**Gegevens** | **Opdrachten**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Aantal processorkernen |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Toegewezen geheugen | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS-serienummer | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
Opstarttype | [ -d /sys/firmware/efi ] && echo EFI \| \| echo BIOS
Naam/versie van het besturingssysteem | We hebben toegang tot deze bestanden voor de versie en naam van het besturingssysteem:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architectuur van besturingssysteem | Uname -m
Aantal schijven | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Opstartschijf | df /boot \| sed -n 2p \| awk '{print $1}'
Schijfgrootte | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Lijst met NIC's | ip -o -4 addr show \| awk '{print $2}'
IP-adres van NIC | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
MAC-adres van NIC | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows-prestatiegegevens

Dit zijn de prestatiegegevens van de Windows-server die het apparaat verzamelt en naar Azure verzendt.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
CPU-gebruik | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Geheugengebruik | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Aantal NIC's | Win32_PerfFormattedData_Tcpip_NetworkInterface | Haal het aantal netwerkapparaat op.
Gegevens ontvangen per NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Gegevens verzonden per NIC | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Aantal schijven | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Aantal schijven
Schijfdetails | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-prestatiegegevens

Dit zijn de prestatiegegevens van de Linux-server die het apparaat verzamelt en naar Azure verzendt.

| **Gegevens** | **Opdrachten** |
| --- | --- |
| CPU-gebruik | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| Geheugengebruik | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| Aantal NIC's | lshw -class network \| grep eth[0-60] \| wc -l |
| Gegevens ontvangen per NIC | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Verzonden gegevens per NIC | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Aantal schijven | fdisk -l \| egrep 'Schijf. \* bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| Schijfdetails | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Apparaatupgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate services die op het apparaat worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch, omdat automatisch bijwerken standaard is ingeschakeld op het apparaat. U kunt deze standaardinstelling wijzigen om de apparaatservices handmatig bij te werken.

### <a name="turn-off-auto-update"></a>Automatisch bijwerken uitschakelen

1. Open registereditor op de server met het apparaat.
2. Navigeer **naarHKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Als u automatisch bijwerken wilt uitschakelen, maakt u een registersleutel **AutoUpdate-sleutel** met de DWORD-waarde 0.

    ![Registersleutel instellen](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Automatisch bijwerken in-/in-/uit

U kunt automatisch bijwerken met een van de volgende methoden in- en uit:

- Door de registersleutel AutoUpdate te verwijderen uit HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Klik op **Apparaatservices weergeven** in de meest recente updatecontroles in het deelvenster Vereisten **instellen** om automatisch bijwerken in te stellen.

De registersleutel verwijderen:

1. Open de Register-editor op de server met het apparaat.
2. Navigeer **naarHKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Verwijder de registersleutel **AutoUpdate,** die eerder is gemaakt om automatisch bijwerken uit te schakelen.

Als u apparaat wilt in Configuration Manager, nadat de detectie is voltooid:

1. Ga in configuration manager van het apparaat naar **het deelvenster Vereisten** instellen
2. Klik in de laatste updatecontrole op **Apparaatservices weergeven** en klik op de koppeling om automatisch bijwerken in te schakel.

    ![Automatische updates in-](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>De versie van de apparaatservices controleren

U kunt de versie van de apparaatservices controleren met behulp van een van deze methoden:

- Ga in Apparaatconfiguratiebeheer naar **het deelvenster Vereisten** instellen.
- Op het apparaat in de **Configuratiescherm**  >  **Programma's en onderdelen.**

Ga als volgende te werk om het apparaatconfiguratiebeheer te controleren:

1. Ga in configuration manager van het apparaat naar **het deelvenster Vereisten** instellen
2. Klik in de laatste updatecontrole op **Apparaatservices weergeven.**

    ![Versie controleren](./media/migrate-appliance/versions.png)

Ga als volgende te werk om de Configuratiescherm:

1. Klik op het apparaat op **Start**  >  **Configuratiescherm**  >  **programma's en onderdelen**
2. Controleer de versies van de apparaatservices in de lijst.

    ![Controleer de versie in Configuratiescherm](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Een oudere versie handmatig bijwerken

Als u een oudere versie voor een van de services gebruikt, moet u de service verwijderen en handmatig bijwerken naar de nieuwste versie.

1. Als u wilt controleren op de meest recente serviceversies van het apparaat, [downloadt](https://aka.ms/latestapplianceservices) LatestComponents.json-file.
2. Nadat u het bestand hebt gedownload, opent LatestComponents.jsin Kladblok.
3. Zoek de nieuwste serviceversie in het bestand en de downloadkoppeling voor het bestand. Bijvoorbeeld:

    "Name": "ASRMigrationWebApp", "DownloadLink": https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Download de nieuwste versie van een verouderde service met behulp van de downloadkoppeling in het bestand.
5. Voer na het downloaden de volgende opdracht uit in een administratoropdrachtvenster om de integriteit van de gedownloade MSI te controleren.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Bijvoorbeeld: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Controleer of de uitvoer van de opdracht overeenkomt met de hash-waarde voor de service in het bestand (bijvoorbeeld de bovenstaande MD5-hashwaarde).
6. Voer nu de MSI uit om de service te installeren. Het is een installatie op de stille installatie en het installatievenster wordt gesloten nadat dit is gedaan.
7. Nadat de installatie is voltooid, controleert u de versie van de service in **configuratiescherm**  >  **Programma's en onderdelen.** De serviceversie moet nu worden bijgewerkt naar de nieuwste versie die wordt weergegeven in het json-bestand.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](how-to-set-up-appliance-vmware.md) het instellen van het apparaat voor VMware.
- [Meer informatie over](how-to-set-up-appliance-hyper-v.md) het instellen van het apparaat voor Hyper-V.
- [Meer informatie over](how-to-set-up-appliance-physical.md) het instellen van het apparaat voor fysieke servers.
