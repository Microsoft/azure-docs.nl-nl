---
title: Releasenotities voor de Azure File Sync agent | Microsoft Docs
description: Lees de releasenotities voor de Azure File Sync agent, waarmee u de bestands shares van uw organisatie kunt centraliseren in Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796688"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Releaseopmerkingen voor de Azure File Sync-agent
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Uw installaties van Windows Server worden getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als u waar ook ter wereld nodig hebt.

Dit artikel bevat de releaseopmerkingen voor de ondersteunde versies van de Azure File Sync-agent.

## <a name="supported-versions"></a>Ondersteunde versies
De volgende Azure File Sync agentversies worden ondersteund:

| Mijlpaal | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| V12 Release - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 maart 2021 | Ondersteund - Flighting |
| Versie V11.3 - [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7 april 2021 | Ondersteund |
| Versie V11.2 - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 februari 2021 | Ondersteund |
| Versie V11.1 - [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 november 2020 | Ondersteund |
| Versie V10.1 - [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 juni 2020 | Ondersteund: agentversie verloopt op 7 juni 2021 |
| Update rollup van mei 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 mei 2020 | Ondersteund: agentversie verloopt op 7 juni 2021 |
| V10-release - [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 april 2020 | Ondersteund: agentversie verloopt op 7 juni 2021 |

## <a name="unsupported-versions"></a>Niet-ondersteunde versies
De volgende Azure File Sync agentversies zijn verlopen en worden niet meer ondersteund:

| Mijlpaal | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| V9-release | 9.0.0.0 - 9.1.0.0 | N.v.t. | Niet ondersteund: agentversie is verlopen op 16 februari 2021 |
| V8-release | 8.0.0.0 | N.v.t. | Niet ondersteund: agentversie is verlopen op 12 januari 2021 |
| V7-release | 7.0.0.0 - 7.2.0.0 | N.v.t. | Niet ondersteund - Agentversies verlopen op 1 september 2020 |
| V6-release | 6.0.0.0 - 6.3.0.0 | N.v.t. | Niet ondersteund- Agentversies verlopen op 21 april 2020 |
| V5-release | 5.0.2.0 - 5.2.0.0 | N.v.t. | Niet ondersteund- Agentversies verlopen op 18 maart 2020 |
| V4-release | 4.0.1.0 - 4.3.0.0 | N.v.t. | Niet ondersteund- Agentversies verlopen op 6 november 2019 |
| V3-release | 3.1.0.0 - 3.4.0.0 | N.v.t. | Niet ondersteund- Agentversies verlopen op 19 augustus 2019 |
| Pre-GA-agents | 1.1.0.0 - 3.0.13.0 | N.v.t. | Niet ondersteund: agentversies zijn verlopen op 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Agentversie 12.0.0.0
De volgende releasenotities zijn voor versie 12.0.0.0 van de Azure File Sync-agent (uitgebracht op 26 maart 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost
- Nieuwe portalervaring voor het configureren van netwerktoegangsbeleid en privé-eindpuntverbindingen
    - U kunt nu de portal gebruiken om de toegang tot het openbare eindpunt van de opslagsynchronisatieservice uit te schakelen en om privé-eindpuntverbindingen goed te keuren, af te wijzen en te verwijderen. Als u het netwerktoegangsbeleid en privé-eindpuntverbindingen wilt configureren, opent u de portal opslagsynchronisatieservice, gaat u naar de sectie Instellingen en klikt u op Netwerk.
 
- Ondersteuning voor cloudopslaglagen voor volumeclustergrootten groter dan 64KiB
    - Cloudopslaglagen ondersteunen nu volumeclustergrootten tot 2MiB op Server 2019. Zie Wat is de minimale bestandsgrootte voor een bestand dat moet worden [opgeslagen in een laag? voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier)
 
- Bandbreedte en latentie meten voor Azure File Sync service- en opslagaccount
    - De Test-StorageSyncNetworkConnectivity cmdlet kan nu worden gebruikt om de latentie en bandbreedte voor de Azure File Sync service en het opslagaccount te meten. De latentie voor Azure File Sync service en opslagaccount wordt standaard gemeten bij het uitvoeren van de cmdlet.  De bandbreedte voor uploaden en downloaden naar het opslagaccount wordt gemeten wanneer u de parameter -MeasureBandwidth gebruikt.
 
        Voer bijvoorbeeld de volgende PowerShell-opdrachten uit om de bandbreedte en latentie voor Azure File Sync service- en opslagaccount te meten:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Verbeterde foutberichten in de portal wanneer het maken van het server-eindpunt mislukt
    - We hebben uw feedback gehoord en hebben de foutberichten en richtlijnen verbeterd wanneer het maken van server-eindpunten mislukt.
 
- Diverse prestatie- en betrouwbaarheidsverbeteringen
    - Verbeterde prestaties van wijzigingsdetectie voor het detecteren van bestanden die zijn gewijzigd in de Azure-bestands share.
    - Prestatieverbeteringen voor afstemmingssynchronisatiesessies. 
    - Synchronisatieverbeteringen om ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED en ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED verminderen.
    - Er is een fout opgelost waardoor gegevens beschadigd zijn als opslag in cloudlagen is ingeschakeld en gelaagde bestanden worden gekopieerd met behulp van Robocopy met de parameter /B.
    - Er is een fout opgelost die ertoe kan leiden dat bestanden niet in een laag op Server 2019 worden opgeslagen als Gegevens ontdubbeling is ingeschakeld op het volume.
    - Er is een fout opgelost die ertoe kan leiden dat AFSDiag bestanden niet kan comprimeren als een bestand groter is dan 2GiB.

### <a name="evaluation-tool"></a>Evaluatieprogramma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van Azure File Sync evaluatieprogramma. Dit hulpprogramma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie de sectie Evaluation [Tool](file-sync-planning.md#evaluation-cmdlet) in de planningshandleiding voor installatie- en gebruiksinstructies. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie Planning for [an Azure File Sync deployment](file-sync-planning.md) (Een Azure File Sync-implementatie plannen) en How to deploy Azure File Sync (Een Azure File Sync-implementatie plannen) voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows [Server.](file-sync-deployment-guide.md)

- Een herstart is vereist voor servers met een bestaande Azure File Sync agentinstallatie.
- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (beheerder).
- De agent wordt niet ondersteund in de nanoserverimplementatieoptie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine met ingeschakeld dynamisch geheugen, moet de virtuele machine worden geconfigureerd met minimaal 2048 MiB aan geheugen. Zie [Aanbevolen systeemresources](file-sync-planning.md#recommended-system-resources) voor meer informatie.
- De Service Opslagsynchronisatieagent (FileSyncSvc) biedt geen ondersteuning voor server-eindpunten op een volume met de SVI-map (System Volume Information) gecomprimeerd. Deze configuratie leidt tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie Problemen met Azure File Sync voor [meer informatie.](file-sync-troubleshoot.md)
- Bestandsserverscherm Resource Manager (FSRM) kan leiden tot eindeloze synchronisatiefouten wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server Azure File Sync agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na het implementeren van de serverinstallatie-installatie en het voltooien van sysprep mini-setup.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Gids voor probleemoplossing](file-sync-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen op een punt.
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
 
### <a name="server-endpoint"></a>Server-eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla geen pagineringsbestand van het besturingssysteem of de toepassing op in een server-eindpuntlocatie.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eindpunt
- Azure File Sync biedt ondersteuning voor het rechtstreeks wijzigen van de Azure-bestands share. Wijzigingen die in de Azure-bestands share zijn aangebracht, moeten echter eerst worden gedetecteerd door een Azure File Sync taak voor wijzigingsdetectie. Een wijzigingsdetectie job wordt eenmaal per 24 uur gestart voor een cloud-eindpunt. Om bestanden die zijn gewijzigd in de Azure-bestands share onmiddellijk te synchroniseren, kan de PowerShell-cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestands share te starten. Bovendien worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de laatste wijziging van SMB niet bijgewerkt en worden ze niet gezien als een synchronisatiewijziging.
- De opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere resourcegroep, abonnement of Azure AD-tenant. Nadat de opslagsynchronisatieservice of het opslagaccount is verplaatst, moet u de toepassing Microsoft.StorageSync toegang geven tot het opslagaccount (zie Ensure Azure File Sync has access to the storage account (Controleren of Azure File Sync toegang heeft tot het [opslagaccount).](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Bij het maken van het cloud-eindpunt moeten de opslagsynchronisatieservice en het opslagaccount zich in dezelfde Azure AD-tenant hebben. Zodra het cloud-eindpunt is gemaakt, kunnen de opslagsynchronisatieservice en het opslagaccount worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met Robocopy, gebruikt u de optie /COPY om tijdstempels van bestanden te behouden. Dit zorgt ervoor dat oudere bestanden eerder dan onlangs toegankelijk zijn gelaagd.

## <a name="agent-version-11300"></a>Agentversie 11.3.0.0
De volgende releasenotities zijn voor versie 11.3.0.0 van de Azure File Sync-agent die op 7 april 2021 is uitgebracht. Deze opmerkingen zijn een aanvulling op de releaseop opmerkingen die worden vermeld voor versie 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost 
Er is een fout opgelost waardoor gegevens beschadigd zijn als opslag in cloudlagen is ingeschakeld en gelaagde bestanden worden gekopieerd met behulp van Robocopy met de parameter /B.

## <a name="agent-version-11200"></a>Agentversie 11.2.0.0
De volgende releasenotities zijn voor versie 11.2.0.0 van de Azure File Sync-agent die op 2 februari 2021 is uitgebracht. Deze opmerkingen zijn een aanvulling op de releaseopgaves die worden vermeld voor versie 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost 
- Als een synchronisatiesessie wordt geannuleerd vanwege een groot aantal fouten per item, kan de synchronisatie worden afgestemd wanneer een nieuwe sessie wordt gestart als de Azure File Sync-service vaststelt dat er een aangepaste synchronisatiesessie nodig is om de fouten per item te corrigeren.
- Het registreren van een server met de Register-AzStorageSyncServer-cmdlet kan mislukken met de fout 'Onverhandelde uitzondering'.
- Nieuwe PowerShell-cmdlet (Add-StorageSyncAllowedServerEndpointPath) om toegestane server-eindpuntpaden op een server te configureren. Deze cmdlet is handig voor scenario's waarin de Azure File Sync-implementatie wordt beheerd door een Cloud Solution Provider (CSP) of serviceprovider en de klant toegestane server-eindpuntpaden op een server wil configureren. Wanneer u een server-eindpunt maakt en het opgegeven pad niet in de allowlist staat, mislukt het maken van het server-eindpunt. Opmerking: dit is een optionele functie en alle ondersteunde paden zijn standaard toegestaan bij het maken van een server-eindpunt.  

    
    - Als u een pad naar een server-eindpunt wilt toevoegen dat is toegestaan, voert u de volgende PowerShell-opdrachten uit op de server:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Voer de volgende PowerShell-opdracht uit om de lijst met ondersteunde paden op te halen:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Als u een pad wilt verwijderen, moet u de volgende PowerShell-opdracht uitvoeren:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Agentversie 11.1.0.0
De volgende releasenotities zijn voor versie 11.1.0.0 van de Azure File Sync-agent (uitgebracht op 4 november 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost
- Nieuwe opslagmodi voor cloudlagen om het initiële downloaden en proactief terughalen te controleren
    - Eerste downloadmodus: u kunt nu kiezen hoe u wilt dat uw bestanden in eerste instantie worden gedownload naar het nieuwe server-eindpunt. Wilt u dat al uw bestanden gelaagd zijn of zo veel mogelijk bestanden op uw server worden gedownload op de laatste wijzigingstijdstempel? Dat kunt u doen. Kunt u geen cloudopslaglagen gebruiken? U kunt er nu voor kiezen om gelaagde bestanden op uw systeem te voorkomen. Zie voor meer informatie [de sectie Een server-eindpunt maken](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) in de documentatie Azure File Sync implementeren.
    - Proactieve terugroepmodus: wanneer een bestand wordt gemaakt of gewijzigd, kunt u het proactief terugroepen naar servers die u in dezelfde synchronisatiegroep opgeeft. Hierdoor is het bestand direct beschikbaar voor gebruik op elke server die u hebt opgegeven. Hebben teams over de hele wereld aan dezelfde gegevens gewerkt? Schakel proactieve terugroepen in, zodat wanneer het team de volgende ochtend aankomt, alle bestanden worden gedownload die door een team in een andere tijdzone zijn bijgewerkt en klaar zijn om aan de slag te gaan. Zie Proactief nieuwe en gewijzigde bestanden [terughalen](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) uit een Azure-bestands share in de documentatie Azure File Sync implementeren voor meer informatie.

- Toepassingen uitsluiten van bijhouden van laatste toegangstijd in cloudlagen U kunt toepassingen nu uitsluiten van het bijhouden van de laatste toegangstijd. Wanneer een toepassing toegang heeft tot een bestand, wordt de laatste toegangstijd voor het bestand bijgewerkt in de database voor cloudopslaglagen. Toepassingen die het bestandssysteem scannen, zoals een antivirusprogramma, zorgen ervoor dat alle bestanden dezelfde laatste toegangstijd hebben, wat van invloed is op het moment dat bestanden in lagen worden opgeslagen.

    Als u toepassingen wilt uitsluiten van het bijhouden van de laatste toegangstijd, voegt u de procesnaam toe aan de registerinstelling HeatTrackingProcessNameExclusionList, die zich onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Voorbeeld: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Processen voor gegevensverdubbeling en bestandsserver-Resource Manager (FSRM) worden standaard uitgesloten (in code) en de uitsluitingslijst voor processen wordt elke vijf minuten vernieuwd.

- Diverse prestatie- en betrouwbaarheidsverbeteringen
    - Verbeterde prestaties van wijzigingsdetectie voor het detecteren van bestanden die zijn gewijzigd in de Azure-bestands share.
    - Verbeterde uploadprestaties voor synchronisatie.
    - De eerste upload wordt nu uitgevoerd vanuit een VSS-momentopname, waardoor fouten per item en synchronisatiesessiefouten worden beperkt.
    - Verbeteringen in de betrouwbaarheid van synchronisatie voor bepaalde I/O-patronen.
    - Er is een fout opgelost om te voorkomen dat de synchronisatiedatabase terug in de tijd op failoverclusters gaat wanneer er een failover plaatsvindt.
    - Verbeterde terugroepprestaties bij het openen van een gelaagd bestand.

### <a name="evaluation-tool"></a>Evaluatieprogramma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van Azure File Sync evaluatieprogramma. Dit hulpprogramma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie de sectie Evaluation [Tool](../file-sync/file-sync-planning.md#evaluation-cmdlet) in de planningshandleiding voor installatie- en gebruiksinstructies. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie Planning for [an Azure File Sync deployment](../file-sync/file-sync-planning.md) (Een Azure File Sync-implementatie plannen) en How to deploy Azure File Sync (Een Azure File Sync-implementatie plannen) voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows [Server.](../file-sync/file-sync-deployment-guide.md)

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (beheerder).
- De agent wordt niet ondersteund in de nanoserverimplementatieoptie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine met ingeschakeld dynamisch geheugen, moet de virtuele machine worden geconfigureerd met minimaal 2048 MiB aan geheugen. Zie [Aanbevolen systeemresources](../file-sync/file-sync-planning.md#recommended-system-resources) voor meer informatie.
- De Service Opslagsynchronisatieagent (FileSyncSvc) biedt geen ondersteuning voor server-eindpunten op een volume met de SVI-map (System Volume Information) gecomprimeerd. Deze configuratie leidt tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie Problemen met Azure File Sync voor [meer informatie.](../file-sync/file-sync-troubleshoot.md)
- Bestandsserverscherm Resource Manager (FSRM) kan leiden tot eindeloze synchronisatiefouten wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server Azure File Sync agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na het implementeren van de serverinstallatie-installatie en het voltooien van sysprep mini-setup.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Gids voor probleemoplossing](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen op een punt.
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
 
### <a name="server-endpoint"></a>Server-eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla geen pagineringsbestand van het besturingssysteem of de toepassing op in een server-eindpuntlocatie.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eindpunt
- Azure File Sync biedt ondersteuning voor het rechtstreeks wijzigen van de Azure-bestands share. Wijzigingen die in de Azure-bestands share zijn aangebracht, moeten echter eerst worden gedetecteerd door een Azure File Sync taak voor wijzigingsdetectie. Een wijzigingsdetectie job wordt eenmaal per 24 uur gestart voor een cloud-eindpunt. Om bestanden die zijn gewijzigd in de Azure-bestands share onmiddellijk te synchroniseren, kan de PowerShell-cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestands share te starten. Bovendien worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de laatste wijziging van SMB niet bijgewerkt en worden ze niet gezien als een synchronisatiewijziging.
- De opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere resourcegroep, abonnement of Azure AD-tenant. Nadat de opslagsynchronisatieservice of het opslagaccount is verplaatst, moet u de toepassing Microsoft.StorageSync toegang geven tot het opslagaccount (zie Ervoor zorgen dat Azure File Sync toegang heeft tot het [opslagaccount).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Bij het maken van het cloud-eindpunt moeten de opslagsynchronisatieservice en het opslagaccount zich in dezelfde Azure AD-tenant hebben. Zodra het cloud-eindpunt is gemaakt, kunnen de opslagsynchronisatieservice en het opslagaccount worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met Robocopy, gebruikt u de optie /WIJS om tijdstempels van bestanden te behouden. Dit zorgt ervoor dat oudere bestanden eerder dan onlangs toegankelijk zijn gelaagd.
    > [!Warning]  
    > Robocopy/B-switch wordt niet ondersteund met Azure File Sync. Het gebruik van de Robocopy/B-switch met Azure File Sync server-eindpunt omdat de bron kan leiden tot beschadiging van het bestand.

## <a name="agent-version-10100"></a>Agentversie 10.1.0.0
De volgende releasenotities zijn voor versie 10.1.0.0 van de Azure File Sync-agent die op 5 juni 2020 is uitgebracht. Deze opmerkingen zijn een aanvulling op de releaseopgaves die worden vermeld voor versie 10.0.0.0 en 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor privé-eindpunten in Azure
    - Synchronisatieverkeer naar de opslagsynchronisatieservice kan nu worden verzonden naar een privé-eindpunt. Dit maakt tunneling via een ExpressRoute- of VPN-verbinding mogelijk. Zie [Configuring Azure File Sync network endpoints (Netwerk Azure File Sync eindpunten configureren) voor meer informatie.](../file-sync/file-sync-networking-endpoints.md)
- Metrische gegevens voor gesynchroniseerde bestanden geven nu de voortgang weer terwijl een grote synchronisatie wordt uitgevoerd, in plaats van aan het einde.
- Diverse betrouwbaarheidsverbeteringen voor agentinstallatie, opslag in cloudlagen, synchronisatie en telemetrie

## <a name="agent-version-10020"></a>Agentversie 10.0.2.0
De volgende releasenotities zijn voor versie 10.0.2.0 van de Azure File Sync-agent die is uitgebracht op 19 mei 2020. Deze opmerkingen zijn een aanvulling op de releaseopgaves die worden vermeld voor versie 10.0.0.0.

Probleem opgelost in deze release:  
- Opslagsynchronisatieagent (FileSyncSvc) loopt regelmatig vast na de installatie van de Azure File Sync v10-agent.

> [!Note]  
>Deze release is niet gerouteerd naar servers die zijn geconfigureerd om automatisch bij te werken wanneer er een nieuwe versie beschikbaar komt. Als u deze update wilt installeren, Microsoft Update of Microsoft Update Catalog (zie [KB4522412](https://support.microsoft.com/help/4522412) voor installatie-instructies).

## <a name="agent-version-10000"></a>Agentversie 10.0.0.0
De volgende releasenotities zijn voor versie 10.0.0.0 van de Azure File Sync-agent (uitgebracht op 9 april 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Verbeterde synchronisatie in de portal
    - Met de release van de V10-agent wordt Azure Portal het type synchronisatiesessie dat wordt uitgevoerd, in de Azure Portal. Bijvoorbeeld eerste download, regelmatig downloaden, terughalen op de achtergrond (snelle noodherstelscenario's) en dergelijke. 

- Verbeterde portalervaring voor cloudopslaglagen
    - Als u bestanden hebt die niet in een laag kunnen worden opgeslagen, kunt u nu de laagfouten in de eigenschappen van het server-eindpunt bekijken.
    - Er is aanvullende informatie over cloudopslaglagen beschikbaar voor een server-eindpunt:
        - Lokale cachegrootte
        - Efficiëntie van cachegebruik
        - Beleidsdetails voor cloudopslaglagen: volumegrootte, huidige vrije ruimte of de tijd die het oudste bestand in de lokale cache het laatst heeft gehad.
    - Deze wijzigingen worden in de Azure Portal kort na de eerste release van de V10-agent.

- Ondersteuning voor het verplaatsen van de opslagsynchronisatieservice en/of het opslagaccount naar een andere Azure Active Directory tenant
    - Azure File Sync ondersteunt nu het verplaatsen van de opslagsynchronisatieservice en/of het opslagaccount naar een andere resourcegroep, abonnement of Azure AD-tenant.
    
- Diverse prestatie- en betrouwbaarheidsverbeteringen
    - Wijzigingsdetectie op de Azure-bestands share kan mislukken als er regels voor virtueel netwerk (VNET) en firewallregels zijn geconfigureerd voor het opslagaccount.
    - Minder geheugenverbruik bij inroepen. 
    - Verbeterde prestaties bij het gebruik van de cmdlet [Invoke-AzStorageSyncChangeDetection.](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Andere diverse verbeteringen in de betrouwbaarheid. 
    
### <a name="evaluation-tool"></a>Evaluatieprogramma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van Azure File Sync evaluatieprogramma. Dit hulpprogramma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie de sectie Evaluation Tool in de [planningshandleiding](../file-sync/file-sync-planning.md#evaluation-cmdlet) voor installatie- en gebruiksinstructies. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie Planning for [an Azure File Sync deployment](../file-sync/file-sync-planning.md) (Planning voor een Azure File Sync-implementatie) en How to deploy Azure File Sync (Een Azure File Sync-implementatie plannen) voor meer informatie over het installeren en configureren van de Azure File Sync-agent [met Windows Server.](../file-sync/file-sync-deployment-guide.md)

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (beheerder).
- De agent wordt niet ondersteund in de nanoserverimplementatieoptie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine met ingeschakeld dynamisch geheugen, moet de virtuele machine worden geconfigureerd met minimaal 2048 MiB aan geheugen.
- De Service Opslagsynchronisatieagent (FileSyncSvc) biedt geen ondersteuning voor server-eindpunten op een volume met de SVI-map (System Volume Information) gecomprimeerd. Deze configuratie leidt tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie Problemen met Azure File Sync voor [meer informatie.](../file-sync/file-sync-troubleshoot.md)
- Bestandsserverscherm Resource Manager (FSRM) kan leiden tot eindeloze synchronisatiefouten wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server Azure File Sync agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na het implementeren van de serverinstallatie-installatie en het voltooien van sysprep mini-setup.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Gids voor probleemoplossing](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen op een punt.
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
 
### <a name="server-endpoint"></a>Server-eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla geen wisselbestand van het besturingssysteem of de toepassing op binnen een server-eindpuntlocatie.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eindpunt
- Azure File Sync biedt ondersteuning voor het rechtstreeks wijzigen van de Azure-bestands share. Wijzigingen die in de Azure-bestands share zijn aangebracht, moeten echter eerst door een Azure File Sync worden gedetecteerd. Een wijzigingsdetectie job wordt eenmaal per 24 uur gestart voor een cloud-eindpunt. Om bestanden die zijn gewijzigd in de Azure-bestands share onmiddellijk te synchroniseren, kan de PowerShell-cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestands share te starten. Bovendien worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de laatste wijziging van SMB niet bijgewerkt en worden ze niet gezien als een synchronisatiewijziging.
- De opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere resourcegroep, abonnement of Azure AD-tenant. Nadat de opslagsynchronisatieservice of het opslagaccount is verplaatst, moet u de toepassing Microsoft.StorageSync toegang geven tot het opslagaccount (zie Ensure Azure File Sync has access to the storage account (Controleren of Azure File Sync toegang heeft tot het [opslagaccount).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Bij het maken van het cloud-eindpunt moeten de opslagsynchronisatieservice en het opslagaccount zich in dezelfde Azure AD-tenant hebben. Zodra het cloud-eindpunt is gemaakt, kunnen de opslagsynchronisatieservice en het opslagaccount worden verplaatst naar verschillende Azure AD-tenants.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met Robocopy, gebruikt u de optie /COPY om tijdstempels van bestanden te behouden. Dit zorgt ervoor dat oudere bestanden eerder dan onlangs toegankelijk zijn gelaagd.
