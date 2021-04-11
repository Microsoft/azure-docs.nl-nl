---
title: Release opmerkingen voor de Azure File Sync-agent | Microsoft Docs
description: Lees de release opmerkingen voor de Azure File Sync-agent, waarmee u de bestands shares van uw organisatie in Azure Files kunt centraliseren.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/26/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: a794274248a12af97174dcc4e86bd4231e9d9dda
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727481"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Releaseopmerkingen voor de Azure File Sync-agent
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Uw installaties van Windows Server worden getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als u waar ook ter wereld nodig hebt.

Dit artikel bevat de releaseopmerkingen voor de ondersteunde versies van de Azure File Sync-agent.

## <a name="supported-versions"></a>Ondersteunde versies
De volgende Azure File Sync agent versies worden ondersteund:

| Mijlpalen | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| V12 release- [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 maart 2021 | Ondersteund-Flighting |
| V 11.2 release- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 februari 2021 | Ondersteund |
| V. w release- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 november 2020 | Ondersteund |
| V 10.1 release- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 juni 2020 | Ondersteund: de versie van de agent verloopt op 7 juni 2021 |
| Update pakket van mei 2020- [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 mei 2020 | Ondersteund: de versie van de agent verloopt op 7 juni 2021 |
| V10 toevoegen release- [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 april 2020 | Ondersteund: de versie van de agent verloopt op 7 juni 2021 |

## <a name="unsupported-versions"></a>Niet-ondersteunde versies
De volgende Azure File Sync agent versies zijn verlopen en worden niet meer ondersteund:

| Mijlpalen | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| Release van V9 | 9.0.0.0 - 9.1.0.0 | N.v.t. | Niet ondersteund: de agent versie is verlopen op 16 februari 2021 |
| Release van V8 | 8.0.0.0 | N.v.t. | Niet ondersteund: de agent versie is verlopen op 12 januari 2021 |
| Versie van V7 | 7.0.0.0 - 7.2.0.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 1 september 2020 |
| Release van v6 | 6.0.0.0 - 6.3.0.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 21 april 2020 |
| Versie V5 | 5.0.2.0 - 5.2.0.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 18 maart 2020 |
| V4-release | 4.0.1.0 - 4.3.0.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 6 november 2019 |
| V3-release | 3.1.0.0 - 3.4.0.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 19 augustus 2019 |
| Pre-GA-agents | 1.1.0.0-3.0.13.0 | N.v.t. | Niet ondersteund: agent versies verlopen op 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>12.0.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 12.0.0.0 van de Azure File Sync-agent (uitgebracht op 26 maart 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost
- Nieuwe portal-ervaring voor het configureren van netwerk toegangs beleid en verbindingen met een privé-eind punt
    - U kunt nu de portal gebruiken om de toegang tot het open bare eind punt van de opslag synchronisatie service uit te scha kelen en privé-eindpunt verbindingen goed te keuren, af te wijzen en te verwijderen. Als u het netwerk toegangs beleid en verbindingen met het persoonlijke eind punt wilt configureren, opent u de opslag synchronisatie service-portal, gaat u naar de sectie instellingen en klikt u op netwerk.
 
- Ondersteuning voor Cloud lagen voor volume cluster grootten groter dan 64KiB
    - Cloud lagen ondersteunt nu volume cluster grootten tot 2MiB op server 2019. Zie [Wat is de minimale bestands grootte voor een bestand dat kan worden gelaagd?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier)voor meer informatie.
 
- De band breedte en latentie voor Azure File Sync Service-en opslag account meten
    - De cmdlet Test-StorageSyncNetworkConnectivity kan nu worden gebruikt om de latentie en band breedte te meten aan de Azure File Sync-Service en het opslag account. De latentie voor de Azure File Sync-Service en het opslag account wordt standaard gemeten tijdens het uitvoeren van de cmdlet.  Het uploaden en downloaden van de band breedte naar het opslag account wordt gemeten wanneer u de para meter-MeasureBandwidth gebruikt.
 
        Voer de volgende Power shell-opdrachten uit om de band breedte en latentie voor de Azure File Sync-Service en het opslag account te meten:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Verbeterde fout berichten in de Portal wanneer het server eindpunt kan worden gemaakt
    - We hebben uw feedback gehoord en hebben de fout berichten en richt lijnen verbeterd wanneer het maken van een server eindpunt mislukt.
 
- Diverse verbeteringen in prestaties en betrouw baarheid
    - Verbeterde detectie prestaties voor wijzigingen om bestanden te detecteren die zijn gewijzigd in de Azure-bestands share.
    - Prestatie verbeteringen voor het afstemmen van synchronisatie sessies. 
    - Synchroniseer verbeteringen om ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED en ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED fouten te verminderen.
    - Bestanden kunnen niet op server 2019 worden getierd als Gegevensontdubbeling is ingeschakeld op het volume.
    - AFSDiag kan bestanden niet comprimeren als een bestand groter is dan 2GiB.

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](./storage-sync-files-planning.md#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- De computer moet opnieuw worden opgestart voor servers met een bestaande Azure File Sync agent-installatie.
- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen. Raadpleeg de [Aanbevolen systeem bronnen](./storage-sync-files-planning.md#recommended-system-resources) voor meer informatie.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell [-cmdlet invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep, een abonnement of een Azure AD-Tenant. Nadat de opslag synchronisatie service of het opslag account is verplaatst, moet u de micro soft. StorageSync-toepassing toegang geven tot het opslag account (Zie [controleren of Azure file sync toegang heeft tot het opslag account](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bij het maken van het Cloud eindpunt moeten de opslag synchronisatie service en het opslag account zich in dezelfde Azure AD-Tenant bevinden. Zodra het Cloud eindpunt is gemaakt, kunnen de opslag synchronisatie service en het opslag account worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
    > [!Warning]  
    > Robocopy/switch wordt niet ondersteund met Azure File Sync. Met behulp van de Robocopy/B-switch met een Azure File Sync server-eind punt, omdat de bron kan leiden tot beschadiging van het bestand.

## <a name="agent-version-11200"></a>11.2.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 11.2.0.0 van de Azure File Sync agent die is uitgebracht op 2 februari 2021. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost 
- Als een synchronisatie sessie wordt geannuleerd als gevolg van een groot aantal fouten per item, kan de synchronisatie worden uitgevoerd door de afstemming wanneer een nieuwe sessie wordt gestart als de Azure File Sync-service bepaalt dat er een aangepaste synchronisatie sessie is vereist om de fouten per item te corrigeren.
- Het registreren van een server met behulp van de cmdlet Register-AzStorageSyncServer kan mislukken met de fout ' onverwerkte uitzonde ring '.
- Nieuwe Power shell-cmdlet (add-StorageSyncAllowedServerEndpointPath) om toegestane server eindpunten paden op een server te configureren. Deze cmdlet is handig voor scenario's waarin de Azure File Sync-implementatie wordt beheerd door een Cloud Solution Provider (CSP) of een service provider en de klant de toegestane server eindpunten paden op een server wil configureren. Bij het maken van een server eindpunt, mislukt het maken van het server eindpunt als het opgegeven pad zich niet in de allowlist bevindt. Opmerking: dit is een optionele functie en alle ondersteunde paden zijn standaard toegestaan bij het maken van een server eindpunt.  

    
    - Voer de volgende Power shell-opdrachten uit op de server om een pad naar het server eindpunt toe te voegen dat is toegestaan:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Voer de volgende Power shell-opdracht uit om de lijst met ondersteunde paden op te halen:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Als u een pad wilt verwijderen, voert u de volgende Power shell-opdracht uit:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>11.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 11.1.0.0 van de Azure File Sync agent (uitgebracht 4 november 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost
- Nieuwe Cloud-tierer modi voor het beheren van initiële down loads en proactieve intrekken
    - Modus voor eerste down load: u kunt nu kiezen hoe u wilt dat uw bestanden voor het eerst worden gedownload op het nieuwe server eindpunt. Wilt u dat al uw bestanden of zoveel mogelijk bestanden op uw server worden gelaagd op basis van de laatst gewijzigde tijds tempel? U kunt dat doen. Kunt u Cloud lagen niet gebruiken? U kunt er nu voor kiezen om gelaagde bestanden op uw systeem te voor komen. Zie de sectie [een server eindpunt maken](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) in de documentatie voor het implementeren van Azure File Sync voor meer informatie.
    - Proactieve terugroep modus: wanneer een bestand wordt gemaakt of gewijzigd, kunt u het proactief intrekken naar servers die u opgeeft binnen dezelfde synchronisatie groep. Hierdoor is het bestand op elke opgegeven server gereed voor gebruik beschikbaar. Hebben teams over de hele wereld gewerkt aan dezelfde gegevens? Proactieve intrekken inschakelen zodat wanneer het team de volgende ochtend aankomt, alle bestanden die door een team in een andere tijd zone worden bijgewerkt, worden gedownload en gereed zijn voor gebruik. Zie [proactief nieuwe en gewijzigde bestanden intrekken van een Azure-bestands share](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) in de implementatie Azure file sync-documentatie voor meer informatie.

- Toepassingen uitsluiten van de laatste keer dat de Cloud wordt getraceerd. u kunt nu toepassingen uitsluiten van het bijhouden van tijd van laatste toegang. Wanneer een toepassing toegang heeft tot een bestand, wordt de tijd van laatste toegang voor het bestand bijgewerkt in de Cloud-Tiering-data base. Toepassingen die het bestands systeem scannen als anti virus zorgen ervoor dat alle bestanden dezelfde laatste toegangs tijd hebben die van invloed is op de lagen van de bestanden.

    Als u toepassingen wilt uitsluiten van het bijhouden van de tijd van laatste toegang, voegt u de proces naam toe aan de register instelling HeatTrackingProcessNameExclusionList die zich onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync bevindt.

    Voor beeld: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Gegevensontdubbeling en bestands Server bron beheer (FSRM)-processen worden standaard uitgesloten (vastgelegd in vaste code) en de lijst met uitsluitingen van processen wordt elke vijf minuten vernieuwd.

- Diverse verbeteringen in prestaties en betrouw baarheid
    - Verbeterde detectie prestaties voor wijzigingen om bestanden te detecteren die zijn gewijzigd in de Azure-bestands share.
    - Verbeterde prestaties van synchronisatie uploaden.
    - De eerste upload wordt nu uitgevoerd vanuit een VSS-moment opname, waardoor fouten per item worden verminderd en sessie fouten worden gesynchroniseerd.
    - Betrouwbaarheids verbeteringen voor bepaalde I/O-patronen synchroniseren.
    - Er is een fout opgelost om te voor komen dat de synchronisatie database terugkeert op failover-clusters wanneer een failover optreedt.
    - Verbeterde intrekkings prestaties bij het openen van een gelaagd bestand.

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](./storage-sync-files-planning.md#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen. Raadpleeg de [Aanbevolen systeem bronnen](./storage-sync-files-planning.md#recommended-system-resources) voor meer informatie.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell [-cmdlet invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep, een abonnement of een Azure AD-Tenant. Nadat de opslag synchronisatie service of het opslag account is verplaatst, moet u de micro soft. StorageSync-toepassing toegang geven tot het opslag account (Zie [controleren of Azure file sync toegang heeft tot het opslag account](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bij het maken van het Cloud eindpunt moeten de opslag synchronisatie service en het opslag account zich in dezelfde Azure AD-Tenant bevinden. Zodra het Cloud eindpunt is gemaakt, kunnen de opslag synchronisatie service en het opslag account worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
    > [!Warning]  
    > Robocopy/switch wordt niet ondersteund met Azure File Sync. Met behulp van de Robocopy/B-switch met een Azure File Sync server-eind punt, omdat de bron kan leiden tot beschadiging van het bestand.

## <a name="agent-version-10100"></a>10.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 10.1.0.0 van de Azure File Sync agent die is uitgebracht op 5 juni 2020. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 10.0.0.0 en 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor Azure private Endpoint
    - Het synchroniseren van verkeer naar de opslag synchronisatie service kan nu worden verzonden naar een persoonlijk eind punt. Hierdoor kan tunneling via een ExpressRoute of VPN-verbinding worden ingeschakeld. Zie [configuring Azure file sync Network endpoints](./storage-sync-files-networking-endpoints.md)(Engelstalig) voor meer informatie.
- Bestanden gesynchroniseerde metrische gegevens worden nu weer gegeven wanneer een grote synchronisatie wordt uitgevoerd, in plaats van aan het einde.
- Diverse verbeteringen van de betrouw baarheid van agent installatie, Cloud lagen, synchronisatie en telemetrie

## <a name="agent-version-10020"></a>10.0.2.0 van agent versie
De volgende release opmerkingen zijn voor versie 10.0.2.0 van de Azure File Sync agent die is uitgebracht op 19 mei 2020. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 10.0.0.0.

Probleem opgelost in deze release:  
- Storage Sync agent (FileSyncSvc) wordt regel matig vastlopen nadat de Azure File Sync V10 toevoegen-agent is geïnstalleerd.

> [!Note]  
>Deze release werd niet geflighted naar servers die zijn geconfigureerd om automatisch bij te werken wanneer een nieuwe versie beschikbaar wordt. Als u deze update wilt installeren, gebruikt u Microsoft Update of Microsoft Update catalogus (Zie [KB4522412](https://support.microsoft.com/help/4522412) voor installatie-instructies).

## <a name="agent-version-10000"></a>Agent versie 10.0.0.0
De volgende release opmerkingen zijn voor versie 10.0.0.0 van de Azure File Sync-agent (uitgebracht op 9 april 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Verbeterde voortgang van synchronisatie in de portal
    - Met de agent Azure Portal release van V10 toevoegen wordt binnenkort het type synchronisatie sessie weer gegeven dat wordt uitgevoerd. Bijvoorbeeld eerste down load, regel matig downloaden, op de achtergrond terughalen (snelle herstel na nood geval) en soort gelijke. 

- Verbeterde portal-ervaring voor Cloud lagen
    - Als u bestanden hebt die niet kunnen worden gelaagd of ingetrokken, kunt u nu de laag fouten in de eigenschappen van het server eindpunt bekijken.
    - Er zijn extra Cloud-laag informatie beschikbaar voor een server eindpunt:
        - Grootte van lokale cache
        - Efficiëntie van cache gebruik
        - Details van het Cloud-laag beleid: de volume grootte, de huidige beschik bare ruimte of de tijd van laatste toegang van het oudste bestand in de lokale cache.
    - Deze wijzigingen worden in de Azure Portal kort na de eerste release van de V10 toevoegen-agent verzonden.

- Ondersteuning voor het verplaatsen van de opslag synchronisatie service en/of het opslag account naar een andere Azure Active Directory Tenant
    - Azure File Sync biedt nu ondersteuning voor het verplaatsen van de opslag synchronisatie service en/of het opslag account naar een andere resource groep, een abonnement of een Azure AD-Tenant.
    
- Diverse verbeteringen in prestaties en betrouw baarheid
    - Het wijzigen van de detectie van de Azure-bestands share kan mislukken als er een virtueel netwerk (VNET) en firewall regels zijn geconfigureerd voor het opslag account.
    - Verminderd geheugen gebruik gekoppeld aan intrekken. 
    - Verbeterde prestaties bij het gebruik van de cmdlet [invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Andere verbeteringen op het gebied van de betrouw baarheid. 
    
### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](./storage-sync-files-planning.md#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell [-cmdlet invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep, een abonnement of een Azure AD-Tenant. Nadat de opslag synchronisatie service of het opslag account is verplaatst, moet u de micro soft. StorageSync-toepassing toegang geven tot het opslag account (Zie [controleren of Azure file sync toegang heeft tot het opslag account](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bij het maken van het Cloud eindpunt moeten de opslag synchronisatie service en het opslag account zich in dezelfde Azure AD-Tenant bevinden. Zodra het Cloud eindpunt is gemaakt, kunnen de opslag synchronisatie service en het opslag account worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
