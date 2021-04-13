---
title: Problemen met Azure File Sync | Microsoft Docs
description: Veelvoorkomende problemen oplossen in een implementatie Azure File Sync, die u kunt gebruiken om Windows Server te transformeren naar een snelle cache van uw Azure-bestands share.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/12/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 54a2493d930069142a8cd6965421dd588b8d76b8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366297"
---
# <a name="troubleshoot-azure-file-sync"></a>Problemen met Azure Files Sync oplossen
Gebruik Azure File Sync om de bestands shares van uw organisatie te centraliseren in Azure Files, met behoud van de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt over de hele wereld.

Dit artikel is ontworpen om u te helpen bij het oplossen van problemen die u mogelijk ondervindt met uw Azure File Sync implementatie. We beschrijven ook hoe u belangrijke logboeken van het systeem verzamelt als een dieper onderzoek van het probleem is vereist. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in volgorde van escalatie):

1. [Microsoft Q&Een vragenpagina voor Azure Storage](/answers/products/azure?product=storage).
2. [Azure Files UserVoice .](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Microsoft-ondersteuning. Als u een nieuwe ondersteuningsaanvraag wilt maken, selecteert u in Azure Portal tabblad **Help** de knop **Help en** ondersteuning en selecteert u vervolgens **Nieuwe ondersteuningsaanvraag.**

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, opslag in cloudlagen, enzovoort). Moet ik mijn server-eindpunt verwijderen en opnieuw maken?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agentinstallatie en serverregistratie
<a id="agent-installation-failures"></a>**Installatiefouten van agent oplossen**  
Als de installatie Azure File Sync agent mislukt, moet u bij een opdrachtprompt met verhoogde opdracht de volgende opdracht uitvoeren om logboekregistratie in teschakelen tijdens de installatie van de agent:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Controleer installer.log om de oorzaak van de installatiefout vast te stellen.

<a id="agent-installation-on-DC"></a>**Installatie van agent mislukt op Active Directory-domein Controller**  
Als u de synchronisatieagent probeert te installeren op een Active Directory-domeincontroller waarbij de eigenaar van de PDC-rol zich op een Windows Server 2008 R2- of lager besturingssysteemversie besturingssysteem, kunt u het probleem krijgen waarbij de synchronisatieagent niet kan worden geïnstalleerd.

U kunt dit oplossen door de PDC-rol over te dragen naar een andere domeincontroller met Windows Server 2012 R2 of een recentere versie en vervolgens synchronisatie te installeren.

<a id="parameter-is-incorrect"></a>**Toegang tot een volume op Windows Server 2012 R2 mislukt met fout: De parameter is onjuist**  
Na het maken van een server-eindpunt op Windows Server 2012 R2, treedt de volgende fout op bij het openen van het volume:

driveletter:\ is niet toegankelijk.  
De parameter is onjuist.

U kunt dit probleem oplossen door [KB2919355 te](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) installeren en de server opnieuw op te starten. Als deze update niet wordt geïnstalleerd omdat er al een latere update is geïnstalleerd, gaat u naar Windows Update, installeert u de meest recente updates voor Windows Server 2012 R2 en start u de server opnieuw op.

<a id="server-registration-missing-subscriptions"></a>**Serverregistratie bevat niet alle Azure-abonnementen**  
Wanneer u een server registreert met ServerRegistration.exe, ontbreken er abonnementen wanneer u op de vervolgkeuzepagina Azure-abonnement klikt.

Dit probleem treedt op omdat ServerRegistration.exe alleen abonnementen van de eerste vijf Azure AD-tenants opvraagt. 

Als u de tenantlimiet voor Serverregistratie op de server wilt verhogen, maakt u een DWORD-waarde met de naam ServerRegistrationTenantLimit onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync met een waarde die groter is dan 5.

U kunt dit probleem ook oplossen door de volgende PowerShell-opdrachten te gebruiken om de server te registreren:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Serverregistratie geeft het volgende bericht weer: 'Vereisten ontbreken'**  
Dit bericht wordt weergegeven als de Az- of AzureRM PowerShell-module niet is geïnstalleerd in PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe biedt geen ondersteuning voor PowerShell 6.x. U kunt de cmdlet Register-AzStorageSyncServer powershell 6.x gebruiken om de server te registreren.

Voer de volgende stappen uit om de Az- of AzureRM-module te installeren in PowerShell 5.1:

1. Typ **powershell vanaf** een opdrachtprompt met verhoogde bevoegdheid en druk op Enter.
2. Installeer de nieuwste Az- of AzureRM-module door de documentatie te volgen:
    - [Az-module (vereist .NET 4.7.2)](/powershell/azure/install-az-ps)
    - [AzureRM-module](https://go.microsoft.com/fwlink/?linkid=856959)
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren bij een opslagsynchronisatieservice.

<a id="server-already-registered"></a>**Serverregistratie geeft het volgende bericht weer: 'Deze server is al geregistreerd'** 

![Een schermopname van het dialoogvenster Serverregistratie met het foutbericht 'server is al geregistreerd'](media/storage-sync-files-troubleshoot/server-registration-1.png)

Dit bericht wordt weergegeven als de server eerder is geregistreerd bij een opslagsynchronisatieservice. Als u de registratie van de server bij de huidige opslagsynchronisatieservice ongedaan wilt maken en u vervolgens wilt registreren bij een nieuwe opslagsynchronisatieservice, voltooit u de stappen die worden beschreven in Registratie van een [server](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)met Azure File Sync .

Als de server niet wordt vermeld onder Geregistreerde **servers** in de opslagsynchronisatieservice, voert u op de server die u de registratie ongedaan wilt maken de volgende PowerShell-opdrachten uit:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Als de server deel uitmaakt van een cluster, kunt u de optionele parameter *Reset-StorageSyncServer -CleanClusterRegistration* gebruiken om ook de clusterregistratie te verwijderen.

<a id="web-site-not-trusted"></a>**Wanneer ik een server registreer, zie ik talloze reacties over 'website niet vertrouwd'. Waarom?**  
Dit probleem treedt op **wanneer het beleid verbeterde beveiliging Internet Explorer** tijdens de serverregistratie is ingeschakeld. Zie **Internet Explorer** Prepare [Windows Server to use with Azure File Sync (Windows Server](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) voorbereiden voor gebruik met Azure File Sync) en How to deploy Azure File Sync (Windows Server voorbereiden voor gebruik met Azure File Sync) en How to deploy Azure File Sync (Windows Server voorbereiden voor gebruik met [Azure File Sync).](storage-sync-files-deployment-guide.md)

<a id="server-registration-missing"></a>**Server wordt niet vermeld onder geregistreerde servers in de Azure Portal**  
Als een server niet wordt vermeld onder Geregistreerde **servers** voor een opslagsynchronisatieservice:
1. Meld u aan bij de server die u wilt registreren.
2. Open Verkenner en ga vervolgens naar de installatiemap van de opslagsynchronisatieagent (de standaardlocatie is C:\Program Files\Azure\StorageSyncAgent). 
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren bij een opslagsynchronisatieservice.

## <a name="sync-group-management"></a>Synchronisatiegroepsbeheer

### <a name="cloud-endpoint-creation-errors"></a>Fouten bij het maken van cloud-eindpunten

<a id="cloud-endpoint-using-share"></a>**Het maken van een cloud-eindpunt mislukt, met de volgende fout: 'De opgegeven Azure FileShare wordt al gebruikt door een ander CloudEndpoint'**  
Deze fout treedt op wanneer de Azure-bestandsshare al wordt gebruikt door een ander cloudeindpunt. 

Als u dit bericht ziet en de Azure-bestands share momenteel niet wordt gebruikt door een cloud-eindpunt, moet u de volgende stappen uitvoeren om de metagegevens van de Azure File Sync op de Azure-bestands share te verwijderen:

> [!Warning]  
> Het verwijderen van de metagegevens op een Azure-bestands share die momenteel wordt gebruikt door een cloud-eindpunt zorgt ervoor dat Azure File Sync-bewerkingen mislukken. Als u deze bestands share vervolgens gebruikt voor synchronisatie in een andere synchronisatiegroep, is gegevensverlies voor bestanden in de oude synchronisatiegroep bijna zeker.

1. Ga in Azure Portal naar uw Azure-bestands share.  
2. Klik met de rechtermuisknop op de Azure-bestands share en selecteer **vervolgens Metagegevens bewerken.**
3. Klik met de rechtermuisknop **op SyncService** en selecteer **verwijderen.**

<a id="cloud-endpoint-authfailed"></a>**Maken van cloud-eindpunt mislukt, met deze fout: 'AuthorizationFailed'**  
Deze fout treedt op als uw gebruikersaccount niet voldoende rechten heeft om een cloud-eindpunt te maken. 

Als u een cloud-eindpunt wilt maken, moet uw gebruikersaccount de volgende machtigingen voor Microsoft-autorisatie hebben:  
* Lezen: Roldefinitie op halen
* Schrijven: Aangepaste roldefinitie maken of bijwerken
* Lezen: Roltoewijzing krijgen
* Schrijven: Roltoewijzing maken

De volgende ingebouwde rollen hebben de vereiste machtigingen voor Microsoft-autorisatie:  
* Eigenaar
* Beheerder van gebruikerstoegang

Om te bepalen of uw gebruikersaccountrol de vereiste machtigingen heeft:  
1. Selecteer in Azure Portal de **optie Resourcegroepen.**
2. Selecteer de resourcegroep waarin het opslagaccount zich bevindt en selecteer vervolgens **Toegangsbeheer (IAM)**.
3. Selecteer het **tabblad Roltoewijzingen.**
4. Selecteer de **Rol** (bijvoorbeeld Eigenaar of Inzender) voor uw gebruikersaccount.
5. Selecteer Microsoft **Authorization in** de lijst **Resourceprovider.** 
    * **Roltoewijzing** moet **de machtigingen Lezen** **en** Schrijven hebben.
    * **Roldefinitie** moet **de machtigingen Lezen** **en** Schrijven hebben.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Fouten bij het maken en verwijderen van server-eindpunten

<a id="-2134375898"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2134375898 of 0x80c80226)**  
Deze fout treedt op als het pad naar het servereindpunt zich op het systeemvolume bevindt en opslag in cloudlagen is ingeschakeld. Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.

<a id="-2147024894"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2147024894 of 0x80070002)**  
Deze fout treedt op als het opgegeven pad naar het servereindpunt niet geldig is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Opmerking: Azure File Sync biedt geen ondersteuning voor toegewezen stations als een pad naar een servereindpunt.

<a id="-2134375640"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2134375640 of 0x80c80328)**  
Deze fout treedt op als het opgegeven pad naar het servereindpunt niet een NTFS-volume is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Opmerking: Azure File Sync biedt geen ondersteuning voor toegewezen stations als een pad naar een servereindpunt.

<a id="-2134347507"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2134347507 of 0x80c8710d)**  
Deze fout treedt op omdat Azure File Sync geen servereindpunten ondersteunt op volumes die een gecomprimeerde map met informatie over systeemvolumes hebben. Om dit probleem op te lossen, decomprimeert u de map met informatie over het systeemvolume. Als de map met informatie over het systeemvolume de enige map is die op het volume is gecomprimeerd, voert u de volgende stappen uit:

1. Download [het hulpprogramma PsExec.](/sysinternals/downloads/psexec)
2. Voer de volgende opdracht uit vanaf een opdrachtprompt met verhoogde opdracht om een opdrachtprompt te starten die wordt uitgevoerd onder het systeemaccount: **PsExec.exe -i -s -d cmd**
3. Typ in de opdrachtprompt die onder het systeemaccount wordt uitgevoerd, de volgende commando's in en druk op enter:   
    **cd /d "station letter:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2134376345 of 0x80C80067)**  
Deze fout treedt op als de limiet voor het aantal servereindpunten per server is bereikt. Azure File Sync ondersteunt momenteel maximaal 30 servereindpunten per server. Zie schaaldoelen Azure File Sync [meer informatie.](./storage-files-scale-targets.md#azure-file-sync-scale-targets)

<a id="-2134376427"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2134376427 of 0x80c80015)**  
Deze fout treedt op als er al een ander servereindpunt wordt gesynchroniseerd met het opgegeven pad naar het servereindpunt. Azure File Sync biedt geen ondersteuning voor meerdere servereindpunten die dezelfde map of hetzelfde volume synchroniseren.

<a id="-2160590967"></a>**Maken van server-eindpunt mislukt, met deze fout: "MgmtServerJobFailed" (foutcode: -2160590967 of 0x80c80077)**  
Deze fout treedt op als het pad naar het servereindpunt zwevende gelaagde bestanden bevat. Als een servereindpunt onlangs is verwijderd, wacht u tot het opschonen van de zwevende gelaagde bestanden is voltooid. Een gebeurtenis-id 6662 wordt geregistreerd in het gebeurtenislogboek telemetrie zodra de zwevende gelaagde bestanden opschonen is gestart. Een gebeurtenis-id 6661 wordt geregistreerd zodra het opschonen van zwevende gelaagde bestanden is voltooid en een server-eindpunt opnieuw kan worden gemaakt met behulp van het pad. Als het maken van het server-eindpunt mislukt nadat het opschonen van gelaagde bestanden is voltooid of als gebeurtenis-id 6661 niet kan worden gevonden in het gebeurtenislogboek telemetrie vanwege rollover van gebeurtenislogboek, verwijdert u de zwevende gelaagde bestanden door de stappen uit te voeren die worden beschreven in de sectie Gelaagde bestanden zijn niet toegankelijk op de [server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) na het verwijderen van een server-eindpunt.

<a id="-2134347757"></a>**Verwijderen van server-eindpunt mislukt met deze fout: 'MgmtServerJobExpired' (foutcode: -2134347757 of 0x80c87013)**  
Deze fout treedt op als de server offline is of geen netwerkverbinding heeft. Als de server niet meer beschikbaar is, moet u de registratie van de server in het portaal opheffen, waardoor de servereindpunten worden verwijderd. Als u de server-eindpunten wilt verwijderen, volgt u de stappen die worden beschreven in Registratie van een server ongedaan maken [met Azure File Sync.](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)

### <a name="server-endpoint-health"></a>Status van server-eindpunt

<a id="server-endpoint-provisioningfailed"></a>**Kan de eigenschappenpagina van het server-eindpunt niet openen of beleid voor cloudopslaglagen niet bijwerken**  
Dit probleem kan optreden als een beheerbewerking op het server-eindpunt mislukt. Als de eigenschappenpagina van het server-eindpunt niet wordt geopend in de Azure Portal, kan dit probleem worden opgelost door het server endpoint bij te werken met behulp van PowerShell-opdrachten van de server. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Server-eindpunt heeft de status 'Geen activiteit' of 'In behandeling' en de serverstatus op de blade geregistreerde servers is 'Wordt offline weergegeven'**  

Dit probleem kan optreden als het Storage Sync Monitor-proces (AzureStorageSyncMonitor.exe) niet wordt uitgevoerd of als de server geen toegang heeft tot de Azure File Sync service.

Op de server die wordt weergegeven als 'Wordt offline weergegeven' in de portal, bekijkt u gebeurtenis-id 9301 in het telemetriegebeurtenislogboek (onder Applications and Services\Microsoft\FileSync\Agent in Logboeken) om te bepalen waarom de server geen toegang heeft tot de Azure File Sync-service. 

- Als **GetNextJob is voltooid met de status: 0** wordt geregistreerd, kan de server communiceren met de Azure File Sync service. 
    - Open Taakbeheer op de server en controleer of het Storage Sync Monitor-proces (AzureStorageSyncMonitor.exe) actief is. Als het proces niet wordt uitgevoerd, start u om te beginnen de server opnieuw op. Als het probleem niet wordt verholpen bij het opnieuw opstarten van de server, voert u een upgrade naar de laatste versie van de Azure File Sync-[agent](./storage-files-release-notes.md) uit. 

- Als GetNextJob is voltooid met **de status : -2134347756** wordt geregistreerd, kan de server niet communiceren met de Azure File Sync-service vanwege een firewall- of proxy- of TLS-coderingssuiteconfiguratie. 
    - Als de server zich achter een firewall bevindt, controleert u of uitgaand verkeer via poort 443 is toegestaan. Als de firewall verkeer beperkt tot specifieke domeinen, controleert u of de domeinen die worden vermeld in de [firewalldocumentatie](./storage-sync-files-firewall-and-proxy.md#firewall) toegankelijk zijn.
    - Als de server zich achter een proxy, configureert u de proxyinstellingen voor de hele machine of app door de stappen in de proxydocumentatie te [volgen.](./storage-sync-files-firewall-and-proxy.md#proxy)
    - Gebruik de Test-StorageSyncNetworkConnectivity om de netwerkverbinding met de service-eindpunten te controleren. Zie Test network [connectivity to service endpoints (Netwerkverbinding met service-eindpunten testen) voor meer informatie.](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)
    - Als de volgorde van de TLS-coderingssuite is geconfigureerd op de server, kunt u groepsbeleid of TLS-cmdlets gebruiken om coderingssuites toe te voegen:
        - Zie [Configuring TLS Cipher Suite Order by using groepsbeleid (TLS Cipher Suite-order](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)configureren met behulp van groepsbeleid ).
        - Zie [TLS Cipher Suite Order configureren met TLS PowerShell-cmdlets als u TLS-cmdlets wilt gebruiken.](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)
    
        Azure File Sync ondersteunt momenteel de volgende coderingssuites voor het TLS 1.2-protocol:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Als GetNextJob is voltooid met **de status : -2134347764** wordt geregistreerd, kan de server niet communiceren met de Azure File Sync-service vanwege een verlopen of verwijderd certificaat.  
    - Voer de volgende PowerShell-opdracht uit op de server om het certificaat dat wordt gebruikt voor verificatie opnieuw in te stellen:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Server-eindpunt heeft de status 'Geen activiteit' en de serverstatus op de blade geregistreerde servers is Online**  

Als een servereindpunt de status 'Geen activiteit' heeft, betekent dit dat het servereindpunt de afgelopen twee uur geen synchronisatieactiviteit heeft gelogd.

Als u de huidige synchronisatieactiviteit op een server wilt controleren, Hoe kan ik [de voortgang van een huidige synchronisatiesessie controleren?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Een server-eindpunt kan synchronisatieactiviteiten mogelijk enkele uren niet in een logboek houden vanwege een fout of onvoldoende systeemresources. Controleer of de meest recente Azure File Sync [agentversie](./storage-files-release-notes.md) is geïnstalleerd. Als het probleem zich blijft voordoen, opent u een ondersteuningsaanvraag.

> [!Note]  
> Als de status van de server op de blade geregistreerde servers 'Wordt offline weergegeven' is, voert u de stappen uit die worden beschreven in Het server-eindpunt heeft de status 'Geen activiteit' of 'In behandeling' en is de serverstatus op de geregistreerde servers de sectie ['Wordt offline](#server-endpoint-noactivity) weergegeven'.

## <a name="sync"></a>Synchroniseren
<a id="afs-change-detection"></a>**Als ik een bestand rechtstreeks in mijn Azure-bestands share heb gemaakt via SMB of via de portal, hoe lang duurt het dan voordat het bestand is gesynchroniseerd met servers in de synchronisatiegroep?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**De status van het server-eindpunt is enkele uren in behandeling**  
Dit probleem wordt verwacht als u een cloud-eindpunt maakt en een Azure-bestands share gebruikt die gegevens bevat. De wijzigingsinumeratie-taak die scant op wijzigingen in de Azure-bestands share moet worden voltooid voordat bestanden kunnen worden gesynchroniseerd tussen de cloud- en server-eindpunten. De tijd voor het voltooien van de taak is afhankelijk van de grootte van de naamruimte in de Azure-bestands share. De status van het server-eindpunt moet worden bijgewerkt zodra de wijzigingsinseratie is voltooid.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hoe kan ik de synchronisatie status controleren?
# <a name="portal"></a>[Portal](#tab/portal1)
Binnen elke synchronisatiegroep kunt u inzoomen op de afzonderlijke server-eindpunten om de status van de laatste voltooide synchronisatiesessies te bekijken. Een groene kolom Status en de waarde Bestanden worden niet gesynchroniseerd 0 geven aan dat de synchronisatie werkt zoals verwacht. Als dit niet het geval is, bekijkt u hieronder een lijst met veelvoorkomende synchronisatiefouten en hoe u bestanden verwerkt die niet worden gesynchroniseerd. 

![Een schermopname van de Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Ga naar de telemetrielogboeken van de server, die u kunt vinden in de Logboeken op `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Gebeurtenis 9102 komt overeen met een voltooide synchronisatiesessie; Zoek voor de meest recente synchronisatiestatus naar de meest recente gebeurtenis met id 9102. SyncDirection geeft aan of deze sessie een upload of download is. Als de HResult 0 is, is de synchronisatiesessie geslaagd. Een HResult die niet nul is, betekent dat er een fout is opgetreden tijdens de synchronisatie; zie hieronder voor een lijst met veelvoorkomende fouten. Als de PerItemErrorCount groter is dan 0, betekent dit dat sommige bestanden of mappen niet goed zijn gesynchroniseerd. Het is mogelijk om een HResult van 0 te hebben, maar een PerItemErrorCount die groter is dan 0.

Hieronder vindt u een voorbeeld van een geslaagde upload. Omwille van de beknoptheid worden hieronder slechts enkele waarden vermeld die zijn opgenomen in elke 9102-gebeurtenis. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Een mislukte upload kan er daarentegen als de volgende uitzien:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Soms mislukken synchronisatiesessies in het algemeen of hebben ze een niet-nul PerItemErrorCount, maar maken ze nog steeds voortgang, met een aantal bestanden die zijn gesynchroniseerd. Dit is te zien in de velden Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes), die u laten zien hoeveel van de sessie slaagt. Als u meerdere synchronisatiesessies in een rij ziet die mislukken, maar een toenemend aantal Toegepaste* hebben, moet u synchronisatietijd geven om het opnieuw te proberen voordat u een ondersteuningsticket opent.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hoe controleer ik de voortgang van een synchronisatiesessie?
# <a name="portal"></a>[Portal](#tab/portal1)
Ga in uw synchronisatiegroep naar het server-eindpunt in kwestie en bekijk de sectie Synchronisatieactiviteit om het aantal bestanden te zien dat is geüpload of gedownload in de huidige synchronisatiesessie. Houd er rekening mee dat deze status ongeveer 5 minuten achterloopt, en dat als uw synchronisatiesessie klein genoeg is om binnen deze periode te worden voltooid, deze mogelijk niet in de portal wordt gerapporteerd. 

# <a name="server"></a>[Server](#tab/server)
Bekijk de meest recente 9302-gebeurtenis in het telemetrielogboek op de server (ga in de Logboeken naar Logboeken Toepassingen en services\Microsoft\FileSync\Agent\Telemetry). Deze gebeurtenis geeft de status van de huidige synchronisatiesessie aan. TotalItemCount geeft aan hoeveel bestanden moeten worden gesynchroniseerd, AppliedItemCount het aantal bestanden dat tot nu toe is gesynchroniseerd en PerItemErrorCount het aantal bestanden dat niet kan worden gesynchroniseerd (zie hieronder voor hoe u dit kunt oplossen).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hoe weet ik of mijn servers met elkaar zijn gesynchroniseerd?
# <a name="portal"></a>[Portal](#tab/portal1)
Zorg ervoor dat voor elke server in een bepaalde synchronisatiegroep:
- De tijdstempels voor de laatste synchronisatiepoging voor zowel uploaden als downloaden zijn recent.
- De status is groen voor zowel uploaden als downloaden.
- In het veld Synchronisatieactiviteit ziet u nog maar weinig of geen bestanden die moeten worden gesynchroniseerd.
- Het veld Bestanden worden niet gesynchroniseerd is 0 voor zowel uploaden als downloaden.

# <a name="server"></a>[Server](#tab/server)
Bekijk de voltooide synchronisatiesessies, die zijn gemarkeerd met 9102-gebeurtenissen in het telemetriegebeurtenislogboek voor elke server (ga in de Logboeken naar `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Op een bepaalde server wilt u controleren of de meest recente upload- en downloadsessies zijn voltooid. Controleer hiervoor of de HResult en PerItemErrorCount 0 zijn voor zowel uploaden als downloaden (het veld SyncDirection geeft aan of een bepaalde sessie een upload- of downloadsessie is). Als u geen onlangs voltooide synchronisatiesessie ziet, wordt er waarschijnlijk een synchronisatiesessie uitgevoerd. Dit is te verwachten als u zojuist een grote hoeveelheid gegevens hebt toegevoegd of gewijzigd.
2. Wanneer een server volledig up-to-date is met de cloud en geen wijzigingen heeft om te synchroniseren in beide richtingen, ziet u lege synchronisatiesessies. Deze worden aangegeven door upload- en downloadgebeurtenissen waarin alle Sync*-velden (SyncFileCount, SyncDirCount, SyncTombstoneCount en SyncSizeBytes) nul zijn, wat betekent dat er niets is gesynchroniseerd. Houd er rekening mee dat deze lege synchronisatiesessies mogelijk niet worden uitgevoerd op servers met een hoog verloop, omdat er altijd iets nieuws is om te synchroniseren. Als er geen synchronisatieactiviteit is, moeten deze elke 30 minuten plaatsvinden. 
3. Als alle servers up-to-date zijn met de cloud, wat betekent dat hun recente upload- en downloadsessies lege synchronisatiesessies zijn, kunt u met redelijke zekerheid zeggen dat het systeem als geheel synchroon is. 
    
Houd er rekening mee dat als u wijzigingen rechtstreeks in uw Azure-bestands share hebt aangebracht, Azure File Sync deze wijziging pas detecteert wanneer de wijzigingsinseratie wordt uitgevoerd, wat elke 24 uur gebeurt. Het is mogelijk dat een server zegt dat deze up-to-date is met de cloud wanneer recente wijzigingen die rechtstreeks in de Azure-bestands share zijn aangebracht, ontbreken. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hoe zie ik of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?
Als uw PerItemErrorCount op de server of het aantal bestanden dat niet wordt gesynchroniseerd in de portal groter is dan 0 voor een bepaalde synchronisatiesessie, betekent dit dat sommige items niet kunnen worden gesynchroniseerd. Bestanden en mappen kunnen kenmerken hebben waardoor ze niet kunnen worden gesynchroniseerd. Deze kenmerken kunnen permanent zijn en vereisen expliciete actie om de synchronisatie te hervatten, bijvoorbeeld het verwijderen van niet-ondersteunde tekens uit de bestands- of mapnaam. Ze kunnen ook tijdelijk zijn, wat betekent dat de synchronisatie van het bestand of de map automatisch wordt hervat; Bestanden met geopende grepen worden bijvoorbeeld automatisch gesynchroniseerd wanneer het bestand wordt gesloten. Wanneer de Azure File Sync een dergelijk probleem detecteert, wordt er een foutenlogboek geproduceerd dat kan worden geparseerd om de items weer te laten zien die momenteel niet goed worden gesynchroniseerd.

Als u deze fouten wilt zien, moet u het powershell-script **vanFileSyncErrorsReport.ps1** (in de installatiemap van de agent van de Azure File Sync-agent) uitvoeren om bestanden te identificeren die niet zijn gesynchroniseerd vanwege geopende grepen, niet-ondersteunde tekens of andere problemen. In het veld ItemPath ziet u de locatie van het bestand in relatie tot de hoofdsynchronisatiemap. Zie de lijst met veelvoorkomende synchronisatiefouten hieronder voor herstelstappen.

> [!Note]  
> Als het FileSyncErrorsReport.ps1 retourneert 'Er zijn geen bestandsfouten gevonden' of als er geen fouten per item voor de synchronisatiegroep worden vermeld, is de oorzaak:
>
>- Oorzaak 1: De laatste voltooide synchronisatiesessie heeft geen fouten per item. De portal wordt binnenkort bijgewerkt met 0 bestanden die niet worden gesynchroniseerd. 
>    - Controleer de [gebeurtenis-id 9102](?tabs=server%252cazure-portal#broken-sync) in het telemetriegebeurtenislogboek om te bevestigen dat de PerItemErrorCount 0 is. 
>
>- Oorzaak 2: Het gebeurtenislogboek ItemResults op de server dat is verpakt vanwege te veel fouten per item en het gebeurtenislogboek bevat geen fouten meer voor deze synchronisatiegroep.
>    - U kunt dit probleem voorkomen door de grootte van het Gebeurtenislogboek ItemResults te vergroten. Het gebeurtenislogboek ItemResults vindt u onder Logboeken toepassingen en services\Microsoft\FileSync\Agent in Logboeken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Problemen met synchronisatiefouten per bestand/map oplossen
**ItemResults-logboek- synchronisatiefouten per item**  

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Het gelaagde bestand op de server is niet toegankelijk. Dit probleem treedt op als het gelaagde bestand niet is ingetrokken voordat een servereindpunt is verwijderd. | Zie Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een [server-eindpunt om](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)dit probleem op te lossen. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | De bestands- of mapwijziging kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout enkele dagen aanhoudt, gebruikt u FileSyncErrorsReport.ps1 PowerShell-script om te bepalen waarom de afhankelijke map nog niet is gesynchroniseerd. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | De bestands- of mapwijziging kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout enkele dagen aanhoudt, gebruikt u FileSyncErrorsReport.ps1 PowerShell-script om te bepalen waarom de afhankelijke map nog niet is gesynchroniseerd. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | De bestands- of mapwijziging kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd en de synchronisatiesessie is mislukt. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, onderzoekt u de fout in de synchronisatiesessie. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Het bestand of de mapnaam is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie [Niet-ondersteunde tekens verwerken](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | De naam van het bestand of de map is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie [Niet-ondersteunde tekens verwerken](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. Azure File Sync maakt één keer per dag een tijdelijke VSS-momentopname op de server om bestanden met geopende grepen te synchroniseren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Het bestand is gewijzigd, maar de wijziging is nog niet gedetecteerd door synchronisatie. Synchronisatie wordt hersteld nadat deze wijziging is gedetecteerd. | U hoeft geen actie te ondernemen. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Het bestand is verwijderd en de synchronisatie is niet op de hoogte van de wijziging. | U hoeft geen actie te ondernemen. Sync stopt met het registreren van deze fout zodra wijzigingsdetectie detecteert dat het bestand is verwijderd. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Het verwijderen van een bestand of map kan niet worden gesynchroniseerd omdat het item al in de bestemming is verwijderd en sync niet op de hoogte is van de wijziging. | U hoeft geen actie te ondernemen. Sync stopt met het vastleggen van deze fout zodra wijzigingsdetectie wordt uitgevoerd op de bestemming en sync detecteert dat het item is verwijderd. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Het bestand of de map is overgeslagen, maar wordt gesynchroniseerd tijdens de volgende synchronisatiesessie. Als deze fout wordt gerapporteerd bij het downloaden van het item, is het bestand of de mapnaam waarschijnlijk ongeldig. | Er is geen actie vereist als deze fout wordt gerapporteerd bij het uploaden van het bestand. Als de fout wordt gerapporteerd bij het downloaden van het bestand, wijzigt u de naam van het bestand of de map in kwestie. Zie [Niet-ondersteunde tekens verwerken](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Het maken van een bestand of map kan niet worden gesynchroniseerd omdat het item al in de bestemming bestaat en sync niet op de hoogte is van de wijziging. | U hoeft geen actie te ondernemen. Sync stopt met het registreren van deze fout zodra wijzigingsdetectie wordt uitgevoerd op de bestemming en synchronisatie op de hoogte is van dit nieuwe item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Het bestand kan niet worden gesynchroniseerd omdat de limiet voor Azure-bestandsshares is bereikt. | Zie U hebt de sectie Opslaglimiet voor [Azure-bestands share](?tabs=portal1%252cazure-portal#-2134351810) bereikt in de gids voor probleemoplossing om dit probleem op te lossen. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Het bestand wordt versleuteld door een niet-ondersteunde oplossing (zoals NTFS EFS). | Ontsleutel het bestand en gebruik een ondersteunde versleutelingsoplossing. Zie [Oplossingen voor versleuteling](./storage-sync-files-planning.md#encryption) in de planningshandleiding voor een lijst met ondersteunde oplossingen. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Het bestand bevindt zich in een DFS-R alleen-lezen replicatiemap. | Bestand bevindt zich in een DFS-R alleen-lezen replicatiemap. Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie [planningshandleiding](./storage-sync-files-planning.md#distributed-file-system-dfs) voor meer informatie. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand heeft de status Verwijderen in behandeling. | U hoeft geen actie te ondernemen. Het bestand wordt verwijderd zodra alle geopende bestandsing handles zijn gesloten. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Het bestand kan niet worden gesynchroniseerd omdat de instellingen voor de firewall en het virtuele netwerk in het opslagaccount zijn ingeschakeld en de server geen toegang heeft tot het opslagaccount. | Voeg het IP-adres van de server of het virtuele netwerk toe door de stappen te volgen die worden beschreven in de sectie Firewall- en [virtuele-netwerkinstellingen](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) configureren in de implementatiehandleiding. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Het bestand kan niet worden gesynchroniseerd omdat de security descriptor groter is dan de limiet van 64 KiB. | U kunt dit probleem oplossen door ACE’s (Access Control Entries) voor het bestand te verwijderen om de grootte van de security descriptor te verkleinen. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Het bestand kan niet worden gesynchroniseerd vanwege een onverwachte fout. | Als de fout enkele dagen aanhoudt, opent u een ondersteuningscase. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand is gewijzigd tijdens de synchronisatie, dus het moet opnieuw worden gesynchroniseerd. | U hoeft geen actie te ondernemen. |
| 0x80070017 | -2147024873 | ERROR_CRC | Het bestand kan niet worden gesynchroniseerd vanwege een CRC-fout. Deze fout kan optreden als een gelaagd bestand niet is ingeroepen vóór het verwijderen van een server-eindpunt of als het bestand beschadigd is. | Zie Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een [server-eindpunt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) om gelaagde bestanden te verwijderen die zwevend zijn om dit probleem op te lossen. Als de fout zich blijft voordoen na het verwijderen van zwevende gelaagde bestanden, voer [chkdsk](/windows-server/administration/windows-commands/chkdsk) uit op het volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Het bestand kan niet worden gesynchroniseerd omdat het maximum aantal conflictbestanden is bereikt. Azure File Sync ondersteunt 100 conflictbestanden per bestand. Zie voor meer informatie over bestandsconflicten Azure File Sync [VEELGESTELDE VRAGEN.](./storage-files-faq.md#afs-conflict-resolution) | U kunt dit probleem oplossen door het aantal conflictbestanden te verminderen. Het bestand wordt gesynchroniseerd zodra het aantal conflictbestanden kleiner is dan 100. |

#### <a name="handling-unsupported-characters"></a>Niet-ondersteunde tekens verwerken
Als in **FileSyncErrorsReport.ps1** PowerShell-script synchronisatiefouten per item worden weergegeven vanwege niet-ondersteunde tekens (foutcode 0x8007007b of 0x80c80255), moet u de bij de fout opgetreden tekens uit de respectieve bestandsnamen verwijderen of de naam wijzigen. In PowerShell worden deze tekens waarschijnlijk afgedrukt als vraagtekens of lege rechthoeken, omdat de meeste van deze tekens geen standaardcodeering voor visuele elementen hebben. 
> [!Note]  
> Het [evaluatiehulpprogramma](storage-sync-files-planning.md#evaluation-cmdlet) kan worden gebruikt om tekens te identificeren die niet worden ondersteund. Als uw gegevensset meerdere bestanden met ongeldige tekens bevat, gebruikt u het script [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) om de naam van bestanden met niet-ondersteunde tekens te wijzigen.

De onderstaande tabel bevat alle unicodetekens die Azure File Sync nog niet ondersteunen.

| Tekenset | Aantal tekens |
|---------------|-----------------|
| <ul><li>0x0000009D (osc-besturingssysteemopdracht)</li><li>0x00000090 (dcs-tekenreeks voor apparaatbeheer)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (vooraf ingestelde hoge octet)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (Arabische presentatieformulieren-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (speciale) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (niet-character)</li><li>0x0002FFFE - 0x0002FFFF = 2 (niet-character)</li><li>0x0003FFFE - 0x0003FFFF = 2 (niet-character)</li><li>0x0004FFFE - 0x0004FFFF = 2 (niet-character)</li><li>0x0005FFFE - 0x0005FFFF = 2 (niet-character)</li><li>0x0006FFFE - 0x0006FFFF = 2 (niet-character)</li><li>0x0007FFFE - 0x0007FFFF = 2 (niet-character)</li><li>0x0008FFFE - 0x0008FFFF = 2 (niet-character)</li><li>0x0009FFFE - 0x0009FFFF = 2 (niet-character)</li><li>0x000AFFFE - 0x000AFFFF = 2 (niet-character)</li><li>0x000BFFFE - 0x000BFFFF = 2 (niet-character)</li><li>0x000CFFFE - 0x000CFFFF = 2 (niet-character)</li><li>0x000DFFFE - 0x000DFFFF = 2 (niet-character)</li><li>0x000EFFFE - 0x000EFFFF = 2 (niet gedefinieerd)</li><li>0x000FFFFE - 0x000FFFFF = 2 (aanvullend privégebruiksgebied)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Veelvoorkomende synchronisatiefouten
<a id="-2147023673"></a>**De synchronisatiesessie is geannuleerd.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimaal)** | -2147023673 | 
| **Fouttekenreeks** | ERROR_CANCELLED |
| **Herstel vereist** | No |

Synchronisatiesessies kunnen om verschillende redenen mislukken, waaronder de server die opnieuw wordt opgestart of bijgewerkt, VSS-momentopnamen, enzovoort. Hoewel deze fout lijkt te worden nagevolgd, is het veilig om deze fout te negeren, tenzij deze zich gedurende een periode van enkele uren blijft voordoen.

<a id="-2147012889"></a>**Er kan geen verbinding met de service tot stand worden gebracht.**    

| Fout | Code |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimaal)** | -2147012889 | 
| **Fouttekenreeks** | WININET_E_NAME_NOT_RESOLVED |
| **Herstel vereist** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**De gebruikersaanvraag is beperkt door de service.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimaal)** | -2134376372 |
| **Fouttekenreeks** | ECS_E_USER_REQUEST_THROTTLED |
| **Herstel vereist** | No |

Er is geen actie vereist; probeert de server het opnieuw. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364043"></a>**Synchronisatie wordt geblokkeerd totdat de wijzigingsdetectie is voltooid na het herstellen**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimaal)** | -2134364043 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Herstel vereist** | No |

Geen actie vereist. Wanneer een bestand of bestands share (cloud-eindpunt) wordt hersteld met behulp van Azure Backup, wordt de synchronisatie geblokkeerd totdat de wijzigingsdetectie op de Azure-bestands share is voltooid. Wijzigingsdetectie wordt onmiddellijk uitgevoerd zodra het herstellen is voltooid. Hoe lang dit duurt is afhankelijk van het aantal bestanden in de bestandsshare.

<a id="-2147216747"></a>**Sync is mislukt omdat de synchronisatiedatabase is verwijderd.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimaal)** | -2147216747 |
| **Fouttekenreeks** | SYNC_E_METADATA_INVALID_OPERATION |
| **Herstel vereist** | No |

Deze fout treedt doorgaans op wanneer met een back-uptoepassing een VSS-momentopname wordt gemaakt en de Sync-database is verwijderd. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364065"></a>**Sync heeft geen toegang tot de Azure-bestands share die is opgegeven in het cloud-eindpunt.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimaal)** | -2134364065 |
| **Fouttekenreeks** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang kan krijgen tot de Azure-bestandsshare. Dit kan komen doordat de Azure-bestandsshare of het opslagaccount waar deze wordt gehost, niet meer bestaat. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)
3. [Zorg Azure File Sync toegang heeft tot het opslagaccount.](#troubleshoot-rbac)
4. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Sync is mislukt omdat de aanvraag niet is gemachtigd om deze bewerking uit te voeren.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimaal)** | -2134351804 |
| **Fouttekenreeks** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat de Azure File Sync agent niet is gemachtigd voor toegang tot de Azure-bestands share. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Zorg Azure File Sync toegang heeft tot het opslagaccount.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**De gebruikte opslagaccountnaam kan niet worden opgelost.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimaal)** | -2134364064 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Herstel vereist** | Yes |

1. Controleer of u de DNS-naam van de opslag van de server kunt oplossen.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Er is een onbekende fout opgetreden tijdens het openen van het opslagaccount.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimaal)** | -2134364022 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Herstel vereist** | Yes |

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Sync is mislukt omdat het opslagaccount is vergrendeld.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimaal)** | -2134364014 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het opslagaccount een alleen-lezen [resourcevergrendeling heeft.](../../azure-resource-manager/management/lock-resources.md) U kunt dit probleem oplossen door de alleen-lezen resourcevergrendeling voor het opslagaccount te verwijderen. 

<a id="-1906441138"></a>**Sync is mislukt vanwege een probleem met de synchronisatiedatabase.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimaal)** | -1906441138 |
| **Fouttekenreeks** | JET_errWriteConflict |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer er een probleem is met de interne database die wordt gebruikt door Azure File Sync. Als dit probleem zich voordoet, maakt u een ondersteuningsaanvraag. We nemen contact met u op om u te helpen dit probleem op te lossen.

<a id="-2134364053"></a>**De Azure File Sync agentversie die op de server is geïnstalleerd, wordt niet ondersteund.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimaal)** | -2134364053 |
| **Fouttekenreeks** | ECS_E_AGENT_VERSION_BLOCKED |
| **Herstel vereist** | Yes |

Deze fout treedt op als de versie van de Azure File Sync-agent op de server wordt niet ondersteund. U kunt dit probleem oplossen door [een upgrade uit te]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) voeren naar een [ondersteunde agentversie.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**U hebt de opslaglimiet van de Azure-bestands share bereikt.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimaal)** | -2134351810 |
| **Fouttekenreeks** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de opslaglimiet van de Azure-bestandsshare is bereikt. Dit kan gebeuren als er een quotum voor een Azure-bestandsshare is ingesteld of als een gebruikslimiet voor een Azure-bestandsshare is overschreden. Zie de huidige limieten voor een [Azure-bestands](storage-files-scale-targets.md)share voor meer informatie.

1. Navigeer naar de synchronisatiegroep in de opslagsynchronisatieservice.
2. Selecteer het cloud-eindpunt in de synchronisatiegroep.
3. Noteer de naam van de Azure-bestands share in het geopende deelvenster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslagaccount waarnaar wordt verwezen, verwijderd.

    ![Een schermopname van het detailvenster van het cloud-eindpunt met een koppeling naar het opslagaccount.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecteer **Bestanden om** de lijst met bestands shares weer te geven.
6. Klik op de drie puntjes aan het einde van de rij voor de Azure-bestands share waarnaar wordt verwezen door het cloud-eindpunt.
7. Controleer of het **gebruik** lager is dan het **quotum**. Tenzij er een alternatief quotum is opgegeven, komt het quotum overeen met de [maximale grootte van de Azure-bestands share.](storage-files-scale-targets.md)

    ![Een schermopname van de eigenschappen van de Azure-bestands share.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Als de share vol is en er geen quotum is ingesteld, kunt u dit probleem oplossen door elke submap van het huidige servereindpunt te maken in een eigen servereindpunt en een eigen synchronisatiegroep. Zo wordt de verschillende submappen gesynchroniseerd met verschillende Azure-bestandsshares.

<a id="-2134351824"></a>**De Azure-bestands share kan niet worden gevonden.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimaal)** | -2134351824 |
| **Fouttekenreeks** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is. Ga als volgt te werk om het probleem op te lossen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)

Als de Azure-bestands share is verwijderd, moet u een nieuwe bestands share maken en vervolgens de synchronisatiegroep opnieuw maken. 

<a id="-2134364042"></a>**Synchronisatie wordt onderbroken terwijl dit Azure-abonnement wordt onderbroken.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimaal)** | -2134364042 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer het Azure-abonnement is onderbroken. Synchronisatie wordt opnieuw ingeschakeld wanneer het Azure-abonnement wordt hersteld. Zie [Waarom is mijn Azure-abonnement uitgeschakeld en hoe kan ik](../../cost-management-billing/manage/subscription-disabled.md) het opnieuw activeren? voor meer informatie.

<a id="-2134375618"></a>**Voor het opslagaccount is een firewall of virtuele netwerken geconfigureerd.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8033e |
| **HRESULT (decimaal)** | -2134375618 |
| **Fouttekenreeks** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is vanwege een firewall bij een opslagaccount, of omdat het opslagaccount deel uitmaakt van een virtueel netwerk. Controleer of de instellingen voor de firewall en het virtuele netwerk in het opslagaccount juist zijn geconfigureerd. Zie Firewall- en virtuele netwerkinstellingen [configureren voor meer informatie.](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 

<a id="-2134375911"></a>**Sync is mislukt vanwege een probleem met de synchronisatiedatabase.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimaal)** | -2134375911 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Herstel vereist** | No |

Deze fout wordt meestal vanzelf opgelost, en kan optreden wanneer:

* Een groot aantal bestandswijzigingen op de servers in de synchronisatiegroep.
* Een groot aantal fouten in afzonderlijke bestanden en mappen.

Als deze fout langer dan een paar uur aanhoudt, maakt u een ondersteuningsaanvraag. We nemen contact met u op om u te helpen dit probleem op te lossen.

<a id="-2146762487"></a>**De server kan geen beveiligde verbinding tot stand brengen. De cloudservice heeft een onverwacht certificaat ontvangen.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimaal)** | -2146762487 |
| **Fouttekenreeks** | CERT_E_UNTRUSTEDROOT |
| **Herstel vereist** | Yes |

Deze fout kan zich voor doen als uw organisatie een TLS-eindproxy gebruikt of als een kwaadwillende entiteit het verkeer tussen uw server en de Azure File Sync service onderschept. Als u zeker weet dat dit wordt verwacht (omdat uw organisatie een TLS-eindproxy gebruikt), slaat u certificaatverificatie over met een register overschrijven.

1. Maak de registerwaarde SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Start de synchronisatieservice opnieuw op de geregistreerde server.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Door deze registerwaarde in te stellen, accepteert de Azure File Sync-agent elk lokaal vertrouwd TLS/SSL-certificaat bij het overdragen van gegevens tussen de server en de cloudservice.

<a id="-2147012894"></a>**Er kan geen verbinding met de service tot stand worden gebracht.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimaal)** | -2147012894 |
| **Fouttekenreeks** | WININET_E_TIMEOUT |
| **Herstel vereist** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Sync is mislukt vanwege een verificatieprobleem.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimaal)** | -2134375680 |
| **Fouttekenreeks** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Herstel vereist** | Yes |

Deze fout wordt meestal veroorzaakt door een onjuiste servertijd. Als de server wordt uitgevoerd op een virtuele machine, controleert u of de tijd op de host juist is.

<a id="-2134364040"></a>**Sync is mislukt vanwege het verlopen van het certificaat.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimaal)** | -2134364040 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, is verlopen.

Voer de volgende stappen uit om te controleren of het certificaat is verlopen:  
1. Open de MMC-module Certificaten, selecteer Computeraccount en navigeer naar Certificaten (lokale computer)\Persoonlijk\Certificaten.
2. Controleer of het certificaat voor clientverificatie is verlopen.

Als het certificaat voor clientverificatie is verlopen, voert u de volgende stappen uit om het probleem op te lossen:

1. Controleer Azure File Sync agentversie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Sync is mislukt omdat het verificatiecertificaat niet is gevonden.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimaal)** | -2134375896 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, niet is gevonden.

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Controleer Azure File Sync agentversie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Sync is mislukt omdat de verificatie-id niet is gevonden.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimaal)** | -2134364039 |
| **Fouttekenreeks** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het verwijderen van het servereindpunt is mislukt en het eindpunt nu een gedeeltelijk verwijderde status heeft. Probeer het servereindpunt opnieuw te verwijderen om dit probleem op te lossen.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Het volume waar het server-eindpunt zich bevindt, heeft weinig schijfruimte.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimaal)** | -1906441711 |
| **Fouttekenreeks** | JET_errLogDiskFull |
| **Herstel vereist** | Yes |

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8031a |
| **HRESULT (decimaal)** | -2134375654 |
| **Fouttekenreeks** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het volume vol raakt. Deze fout treedt doorgaans op omdat bestanden buiten het servereindpunt ruimte gebruiken op het volume. U kunt ruimte vrij maken op het volume door extra server-eindpunten toe te voegen, bestanden naar een ander volume te verplaatsen of het volume van het server-eindpunt te vergroten.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**De service is nog niet gereed voor synchronisatie met dit server-eindpunt.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimaal)** | -2134364145 |
| **Fouttekenreeks** | ECS_E_REPLICA_NOT_READY |
| **Herstel vereist** | No |

Deze fout treedt op omdat het cloud-eindpunt is gemaakt met inhoud die al op de Azure-bestands share staat. Azure File Sync moet de Azure-bestands share scannen op alle inhoud voordat het server-eindpunt de initiële synchronisatie kan uitvoeren.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Sync is mislukt vanwege problemen met veel afzonderlijke bestanden.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimaal)** | -2134375877 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Herstel vereist** | Yes |

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8021c |
| **HRESULT (decimaal)** | -2134375908 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Herstel vereist** | Yes |

| Fout | Code |
|-|-|
| **Hresult** | 0x80c80253 |
| **HRESULT (decimaal)** | -2134375853 |
| **Fouttekenreeks** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Herstel vereist** | Yes |

Synchronisatiesessies mislukken met een van deze fouten wanneer er veel bestanden zijn die niet kunnen worden gesynchroniseerd met fouten per item. Voer de stappen uit die in de sectie Hoe kan ik of er specifieke bestanden of mappen zijn die niet worden [gesynchroniseerd?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) om de fouten per item op te lossen. Voor synchronisatiefouten ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED, opent u een ondersteuningscase.

> [!NOTE]
> Azure File Sync maakt één keer per dag een tijdelijke VSS-momentopname op de server om bestanden met geopende grepen te synchroniseren.

<a id="-2134376423"></a>**Sync is mislukt vanwege een probleem met het pad naar het server-eindpunt.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimaal)** | -2134376423 |
| **Fouttekenreeks** | ECS_E_SYNC_INVALID_PATH |
| **Herstel vereist** | Yes |

Zorg ervoor dat het pad bestaat, zich op een lokaal NTFS-volume bevindt en geen reparsepunt of bestaand server-eindpunt is.

<a id="-2134375817"></a>**Sync is mislukt omdat de versie van het filter stuurprogramma niet compatibel is met de versie van de agent**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimaal)** | -2134375817 |
| **Fouttekenreeks** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Herstel vereist** | Yes |

Deze fout komt doordat de geladen versie van het filterstuurprogramma voor opslag in cloudlagen (StorageSync.sys) niet compatibel is met de Storage Sync Agent-service (FileSyncSvc). Als de Azure File Sync-agent is bijgewerkt, start u de server opnieuw op om de installatie te voltooien. Als de fout blijft optreden, verwijdert u de agent, start u de server opnieuw op en installeert u de Azure File Sync-agent opnieuw.

<a id="-2134376373"></a>**De service is momenteel niet beschikbaar.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimaal)** | -2134376373 |
| **Fouttekenreeks** | ECS_E_SERVICE_UNAVAILABLE |
| **Herstel vereist** | No |

Deze fout treedt op omdat de Azure File Sync-service niet beschikbaar is. Deze fout wordt automatisch opgelost wanneer de Azure File Sync-service weer beschikbaar is.

<a id="-2146233088"></a>**Sync is mislukt vanwege een uitzondering.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimaal)** | -2146233088 |
| **Fouttekenreeks** | COR_E_EXCEPTION |
| **Herstel vereist** | No |

Deze fout treedt op omdat zich in Sync een uitzondering voordoet. Als de fout enkele uren aanhoudt, maakt u een ondersteuningsaanvraag.

<a id="-2134364045"></a>**Sync is mislukt omdat voor het opslagaccount een fout is overgeslagen naar een andere regio.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimaal)** | -2134364045 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het opslagaccount is overgeschakeld naar een andere regio. Failover-overschakeling van het opslagaccount wordt niet ondersteund in Azure File Sync. Er mag geen failover-overschakeling worden uitgevoerd voor opslagaccounts met Azure-bestandsshares die worden gebruikt als cloudeindpunten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen. U kunt dit probleem oplossen door het opslagaccount te verplaatsen naar de primaire regio.

<a id="-2134375922"></a>**Sync is mislukt vanwege een tijdelijk probleem met de synchronisatiedatabase.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimaal)** | -2134375922 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Herstel vereist** | No |

Deze fout treedt op vanwege een intern probleem met de synchronisatiedatabase. Deze fout wordt automatisch opgelost bij nieuwe synchronisatiepogingen. Als deze fout een langere periode duurt, maakt u een ondersteuningsaanvraag. We nemen contact met u op om u te helpen dit probleem op te lossen.

<a id="-2134364024"></a>**Sync is mislukt vanwege een wijziging in Azure Active Directory tenant**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimaal)** | -2134364024 | 
| **Fouttekenreeks** | ECS_E_INVALID_AAD_TENANT |
| **Herstel vereist** | Yes |

Zorg ervoor dat u de meest recente Azure File Sync hebt. Vanaf agent V10 ondersteunt Azure File Sync het verplaatsen van het abonnement naar een andere Azure Active Directory tenant.
 
Zodra u de nieuwste versie van de agent hebt, moet u de toepassing Microsoft.StorageSync toegang geven tot het opslagaccount (zie Ensure Azure File Sync has access to the storage account (Controleren of Azure File Sync toegang heeft tot [het opslagaccount).](#troubleshoot-rbac)

<a id="-2134364010"></a>**Sync is mislukt omdat er geen firewall- en virtuele netwerk-uitzondering is geconfigureerd**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimaal)** | -2134364010 | 
| **Fouttekenreeks** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Herstel vereist** | Yes |

Deze fout treedt op als de instellingen voor de firewall en het virtuele netwerk zijn ingeschakeld voor het opslagaccount en de uitzondering Vertrouwde Microsoft-services toegang tot dit opslagaccount toestaan niet is ingeschakeld. U kunt dit probleem oplossen door de stappen te volgen die worden beschreven in de sectie [Instellingen voor de firewall en het virtuele netwerk configureren](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatiehandleiding.

<a id="-2147024891"></a>**Sync is mislukt omdat de machtigingen voor de map System Volume Information onjuist zijn.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimaal)** | -2147024891 |
| **Fouttekenreeks** | ERROR_ACCESS_DENIED |
| **Herstel vereist** | Yes |

Deze fout kan optreden als het NT AUTHORITY\SYSTEM-account geen machtigingen heeft voor de map System Volume Information op het volume waar het servereindpunt zich bevindt. Als afzonderlijke bestanden niet kunnen worden gesynchroniseerd met ERROR_ACCESS_DENIED, voert u de stappen uit die worden beschreven in de sectie Problemen per [bestands-/mapsynchronisatie oplossen.](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Download [het hulpprogramma Psexec.](/sysinternals/downloads/psexec)
2. Voer de volgende opdracht uit vanaf een opdrachtprompt met verhoogde opdracht om een opdrachtprompt te starten met het systeemaccount: **PsExec.exe -i -s -d cmd** 
3. Voer de volgende opdracht uit vanaf de opdrachtprompt die wordt uitgevoerd met het systeemaccount om te controleren of het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information: **cacls "stationsletter:\system volume information" /T /C**
4. Als het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information, voert u de volgende opdracht uit: **cacls "stationsletter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM: F"**
    - Als stap 4 mislukt omdat de toegang wordt geweigerd, voert u de volgende opdracht uit om eigenaar van de map System Volume Information te worden en herhaalt u vervolgens stap 4: **takeown /A /R /F "stationsletter:\System Volume Information"**

<a id="-2134375810"></a>**Sync is mislukt omdat de Azure-bestands share is verwijderd en opnieuw is gemaakt.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimaal)** | -2134375810 |
| **Fouttekenreeks** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat Azure File Sync geen ondersteuning biedt voor het verwijderen en opnieuw maken van een Azure-bestandsshare in dezelfde synchronisatiegroep. 

U kunt dit probleem oplossen door de synchronisatiegroep te verwijderen en opnieuw te maken door de volgende stappen uit te voeren:

1. Verwijder alle server-eindpunten in de synchronisatiegroep.
2. Verwijder het cloud-eindpunt. 
3. Verwijder de synchronisatiegroep.
4. Als opslag in cloudlagen is ingeschakeld op een server-eindpunt, verwijdert u de zwevende gelaagde bestanden op de server door de stappen uit te voeren die worden beschreven in de sectie Gelaagde bestanden zijn niet toegankelijk op de [server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) na het verwijderen van een server-eindpunt.
5. Maak de synchronisatiegroep opnieuw.

<a id="-2145844941"></a>**Sync is mislukt omdat de HTTP-aanvraag is omgeleid**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimaal)** | -2145844941 |
| **Fouttekenreeks** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Herstel vereist** | Yes |

Deze fout treedt op Azure File Sync http-omleiding (3xx-statuscode) niet ondersteunt. U kunt dit probleem oplossen door HTTP-omleiding op uw proxyserver of netwerkapparaat uit te schakelen.

<a id="-2134364027"></a>**Er is een time-out opgetreden tijdens offline gegevensoverdracht, maar deze wordt nog uitgevoerd.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimaal)** | -2134364027 |
| **Fouttekenreeks** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Herstel vereist** | No |

Deze fout treedt op wanneer een gegevens opnamebewerking de time-out overschrijdt. Deze fout kan worden genegeerd als de synchronisatie wordt uitgevoerd (AppliedItemCount is groter dan 0). Zie [Hoe kan ik de voortgang van een huidige synchronisatiesessie controleren?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Sync is mislukt omdat het pad naar het server-eindpunt niet kan worden gevonden op de server.**  

| Fout | Code |
|-|-|
| **Hresult** | 0x80c8027a |
| **HRESULT (decimaal)** | -2134375814 |
| **Fouttekenreeks** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op als de naam van de map die wordt gebruikt als het pad naar het server-eindpunt is gewijzigd of verwijderd. Als de naam van de map is gewijzigd, wijzigt u de naam van de map weer in de oorspronkelijke naam en start u de Storage Sync Agent-service (FileSyncSvc) opnieuw.

Als de map is verwijderd, voert u de volgende stappen uit om het bestaande server-eindpunt te verwijderen en een nieuw server-eindpunt te maken met behulp van een nieuw pad:

1. Verwijder het server-eindpunt in de synchronisatiegroep door de stappen te volgen die worden beschreven in [Een server-eindpunt verwijderen.](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)
2. Maak een nieuw server-eindpunt in de synchronisatiegroep door de stappen te volgen die worden beschreven in [Een server-eindpunt toevoegen.](./storage-sync-files-server-endpoint.md#add-a-server-endpoint)

### <a name="common-troubleshooting-steps"></a>Algemene stappen voor probleemoplossing
<a id="troubleshoot-storage-account"></a>**Controleer of het opslagaccount bestaat.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer naar de synchronisatiegroep in de opslagsynchronisatieservice.
2. Selecteer het cloud-eindpunt in de synchronisatiegroep.
3. Noteer de naam van de Azure-bestands share in het geopende deelvenster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslagaccount waarnaar wordt verwezen, verwijderd.
    ![Een schermopname van het detailvenster van het cloud-eindpunt met een koppeling naar het opslagaccount.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Zorg ervoor dat de Azure-bestands share bestaat.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klik **op Overzicht** in de inhoudsopgave aan de linkerkant om terug te keren naar de hoofdpagina van het opslagaccount.
2. Selecteer **Bestanden om** de lijst met bestands shares weer te geven.
3. Controleer of de bestands share waarnaar wordt verwezen door het cloud-eindpunt wordt weergegeven in de lijst met bestands shares (u moet dit in stap 1 hierboven hebben genoteerd).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Zorg Azure File Sync toegang heeft tot het opslagaccount.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klik **op Toegangsbeheer (IAM)** in de inhoudsopgave aan de linkerkant.
1. Klik op **het tabblad Roltoewijzingen** om de gebruikers en toepassingen *(service-principals)* weer te geven die toegang hebben tot uw opslagaccount.
1. Controleer **of Microsoft.StorageSync** of **Hybrid File Sync Service** (oude toepassingsnaam) wordt weergegeven in de lijst met de rol Lezer en **Gegevenstoegang.** 

    ![Een schermopname van de Service-principal File Sync Hybrid File Sync op het tabblad Toegangsbeheer van het opslagaccount](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Als **Microsoft.StorageSync** of **Hybrid File Sync Service** niet wordt weergegeven in de lijst, voert u de volgende stappen uit:

    - Klik op **Add**.
    - Selecteer in **het** veld Rol **de optie Lezer en Gegevenstoegang.**
    - Typ  **Microsoft.StorageSync** in het veld Selecteren, selecteer de rol en klik op **Opslaan.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>Cloudopslaglagen 
Er zijn twee paden voor fouten in cloudopslaglagen:

- Bestanden kunnen niet in een laag worden opgeslagen, wat betekent dat Azure File Sync probeert een bestand in een laag te Azure Files.
- Bestanden kunnen niet worden ingeroepen, wat betekent dat het Azure File Sync-bestandssysteemfilter (StorageSync.sys) geen gegevens kan downloaden wanneer een gebruiker probeert toegang te krijgen tot een bestand dat in een laag is opgeslagen.

Er zijn twee hoofdklassen van fouten die kunnen optreden via een van beide foutpaden:

- Fouten met cloudopslag
    - *Tijdelijke problemen met de beschikbaarheid van opslagservice.* Zie de Service Level Agreement [(SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)voor Azure Storage.
    - *Niet-toegankelijke Azure-bestands share*. Deze fout teert meestal wanneer u de Azure-bestands share verwijdert wanneer deze nog steeds een cloud-eindpunt in een synchronisatiegroep is.
    - *Niet-toegankelijk opslagaccount*. Deze fout teert meestal wanneer u het opslagaccount verwijdert terwijl het nog steeds een Azure-bestands share heeft die een cloud-eindpunt in een synchronisatiegroep is. 
- Serverfouten 
  - *Azure File Sync bestandssysteemfilter (StorageSync.sys) wordt niet geladen.* Als u wilt reageren op aanvragen voor opslaglagen/terugroepen, moet Azure File Sync bestandssysteemfilter worden geladen. Het filter dat niet wordt geladen, kan om verschillende redenen plaatsvinden, maar de meest voorkomende reden is dat een beheerder het handmatig heeft verwijderd. Het Azure File Sync bestandssysteemfilter moet te allen tijde worden geladen om ervoor te Azure File Sync goed werkt.
  - *Ontbrekende, beschadigde of anderszins defecte reparsepunt*. Een reparsepunt is een speciale gegevensstructuur van een bestand dat uit twee delen bestaat:
    1. Een reparse-tag, die aan het besturingssysteem aangeeft dat het Azure File Sync-bestandssysteemfilter (StorageSync.sys) mogelijk actie moet ondernemen op IO naar het bestand. 
    2. Gegevens reparseren, wat aangeeft dat het bestandssysteem de URI van het bestand op het bijbehorende cloud-eindpunt (de Azure-bestands share) filtert. 
        
       De meest voorkomende manier waarop een reparsepunt beschadigd kan raken, is als een beheerder probeert de tag of de gegevens ervan te wijzigen. 
  - *Problemen met de netwerkverbinding*. Als u een bestand in een laag wilt plaatsen of inroepen, moet de server verbinding hebben met internet.

De volgende secties geven aan hoe u problemen met cloudopslaglagen kunt oplossen en hoe u kunt bepalen of een probleem een probleem met cloudopslag of een serverprobleem is.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Activiteit van opslaglagen op een server bewaken  
Gebruik gebeurtenis-id 9003, 9016 en 9029 in het gebeurtenislogboek Telemetrie (onder Toepassingen en services\Microsoft\FileSync\Agent in Logboeken) om laagactiviteit op een server te bewaken.

- Gebeurtenis-id 9003 biedt foutdistributie voor een server-eindpunt. Bijvoorbeeld Totaal aantal fouten, ErrorCode, enzovoort. Houd er rekening mee dat er één gebeurtenis per foutcode wordt geregistreerd.
- Gebeurtenis-id 9016 biedt ghostingresultaten voor een volume. Zo is het percentage vrije ruimte, Aantal bestanden dat in een sessie is gedschaduwd, het aantal bestanden dat niet is gedeerd, enzovoort.
- Gebeurtenis-id 9029 biedt informatie over een ghosting-sessie voor een server-eindpunt. Bijvoorbeeld Aantal bestanden dat is geprobeerd in de sessie, Aantal bestanden in een laag in de sessie, Aantal bestanden dat al in een laag is opgeslagen, enzovoort.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Terughaalactiviteit op een server bewaken
Als u de terugroepactiviteit op een server wilt bewaken, gebruikt u gebeurtenis-id 9005, 9006, 9009 en 9059 in het telemetriegebeurtenislogboek (onder Toepassingen en services\Microsoft\FileSync\Agent in Logboeken).

- Gebeurtenis-id 9005 biedt betrouwbaarheid van terughalen voor een server-eindpunt. Bijvoorbeeld Totaal aantal unieke bestanden dat is gebruikt, Totaal aantal unieke bestanden met mislukte toegang, enzovoort.
- Gebeurtenis-id 9006 biedt distributie van terugroepfouten voor een server-eindpunt. Bijvoorbeeld Totaal aantal mislukte aanvragen, ErrorCode, enzovoort. Houd er rekening mee dat er één gebeurtenis per foutcode wordt geregistreerd.
- Gebeurtenis-id 9009 bevat informatie over de relevante sessie voor een server-eindpunt. Bijvoorbeeld DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, enzovoort.
- Gebeurtenis-id 9059 biedt distributie van terugroepen van toepassingen voor een server-eindpunt. Bijvoorbeeld ShareId, Application Name en TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Problemen met bestanden oplossen die niet in een laag kunnen worden opgeslagen
Als er geen opslaglagen voor bestanden worden Azure Files:

1. Bekijk Logboeken telemetrie, operationele en diagnostische gebeurtenislogboeken onder Toepassingen en services\Microsoft\FileSync\Agent. 
   1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.

      > [!NOTE]
      > Een bestand moet worden gesynchroniseerd met een Azure-bestands share voordat het gelaagd kan worden.

   2. Controleer of de server verbinding heeft met internet. 
   3. Controleer of de Azure File Sync (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
       - Voer uit bij een opdrachtprompt met verhoogde `fltmc` opdracht. Controleer of de stuurprogramma'StorageSync.sys en StorageSyncGuard.sys voor bestandssysteemfilters worden weergegeven.

> [!NOTE]
> Een gebeurtenis-id 9003 wordt eenmaal per uur geregistreerd in het telemetriegebeurtenislogboek als een bestand niet kan worden gelaagd (er wordt één gebeurtenis geregistreerd per foutcode). Controleer de [sectie Fouten en herstel](#tiering-errors-and-remediation) in lagen om te zien of er herstelstappen worden vermeld voor de foutcode.

### <a name="tiering-errors-and-remediation"></a>Fouten in lagen en herstel

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Het bestand kan niet in een laag worden opgeslagen omdat de eerste upload wordt uitgevoerd. | U hoeft geen actie te ondernemen. Het bestand wordt gelaagd zodra de eerste upload is voltooid. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Het bestand kan niet worden gelaagd omdat het in gebruik is. | U hoeft geen actie te ondernemen. Het bestand wordt gelaagd wanneer het niet meer in gebruik is. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Het bestand kan niet in een laag worden opgeslagen omdat het is uitgesloten door synchronisatie. | U hoeft geen actie te ondernemen. Bestanden in de uitsluitingslijst voor synchronisatie kunnen niet worden gelaagd. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | De laag van het bestand is mislukt omdat het niet is gevonden op de server. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u of het bestand op de server bestaat. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Het bestand kan niet worden gelaagd omdat het is verwijderd uit de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand moet worden verwijderd op de server wanneer de volgende synchronisatiesessie wordt uitgevoerd. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | De laag van het bestand is mislukt vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerkverbinding met de Azure-bestands share. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | De laag van het bestand is mislukt vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerkverbinding met de Azure-bestands share. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | De laag van het bestand is mislukt vanwege de fout toegang geweigerd. Deze fout kan optreden als het bestand zich in een DFS-R alleen-lezen replicatiemap bevindt. | Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie [planningshandleiding](./storage-sync-files-planning.md#distributed-file-system-dfs) voor meer informatie. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Het bestand kan niet in een laag worden opgeslagen vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerkverbinding met de Azure-bestands share. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Het bestand kan niet worden gelaagd omdat de bestandsgrootte kleiner is dan de ondersteunde grootte. | Als de versie van de agent kleiner is dan 9.0, is de minimaal ondersteunde bestandsgrootte 64 kB. Als de agentversie 9.0 en hoger is, is de minimaal ondersteunde bestandsgrootte gebaseerd op de clustergrootte van het bestandssysteem (dubbele bestandssysteemclustergrootte). Als de grootte van het bestandssysteemcluster bijvoorbeeld 4 kB is, is de minimale bestandsgrootte 8 kB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | De opslaglaag van het bestand is mislukt vanwege een probleem met Azure Storage. | Als de fout zich blijft voordoen, opent u een ondersteuningsaanvraag. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Het bestand kon niet in een laag worden opgeslagen omdat het op hetzelfde moment werd ingeroepen. | U hoeft geen actie te ondernemen. Het bestand wordt gelaagd wanneer de terugroep is voltooid en het bestand niet meer in gebruik is. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Het bestand kan niet in een laag worden opgeslagen omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt in een laag opgeslagen nadat het is gesynchroniseerd met de Azure-bestands share. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Het bestand kan niet in een laag worden opgeslagen omdat het filter stuurprogramma voor cloudlagen (storagesync.sys) niet wordt uitgevoerd. | U kunt dit probleem oplossen door een opdrachtprompt met verhoogde opdracht te openen en de volgende opdracht uit te voeren: `fltmc load storagesync`<br>Als het stuurprogramma voor het storagesync-filter niet kan worden geladen bij het uitvoeren van de fltmc-opdracht, verwijdert u de Azure File Sync-agent, start u de server opnieuw op en installeert u de Azure File Sync agent. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand kan niet worden gelaagd vanwege onvoldoende schijfruimte op het volume waar het server-eindpunt zich bevindt. | U kunt dit probleem oplossen door ten minste 100 MB aan schijfruimte vrij te maken op het volume waarop het server-eindpunt zich bevindt. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Het bestand kan niet worden gelaagd omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt in een laag opgeslagen nadat het is gesynchroniseerd met de Azure-bestands share. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Het bestand kan niet worden gelaagd omdat het een niet-ondersteund reparsepunt is. | Als het bestand een reparsepunt voor gegevens ontdubbeling [](./storage-sync-files-planning.md#data-deduplication) is, volgt u de stappen in de planningshandleiding om ondersteuning voor gegevensdeduplicatie in teschakelen. Bestanden met andere reparsepunten dan Gegevens ontdubbeling worden niet ondersteund en worden niet gelaagd.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Het bestand kan niet in een laag worden opgeslagen omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt gelaagd zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestands share. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Het bestand kan niet in een laag worden opgeslagen omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt in een laag opgeslagen nadat het is gesynchroniseerd met de Azure-bestands share. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | De laag van het bestand is mislukt vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerkverbinding met de Azure-bestands share. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand kan niet in een laag worden opgeslagen omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt in een laag opgeslagen zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestands share. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand kan niet worden gelaagd vanwege onvoldoende systeembronnen. | Als de fout zich blijft voordoen, moet u onderzoeken welk toepassings- of kernelmodus stuurprogramma systeembronnen uitput. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Het bestand kan niet worden gelaagd vanwege een I/O-fout bij het schrijven naar de database voor cloudopslaglagen. | Als de fout zich blijft voordoen, moet u chkdsk uitvoeren op het volume en de opslaghardware controleren. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Het bestand kan niet worden gelaagd omdat de database voor cloudopslaglagen niet wordt uitgevoerd. | U kunt dit probleem oplossen door de FileSyncSvc-service of -server opnieuw op te starten. Als de fout zich blijft voordoen, moet u chkdsk uitvoeren op het volume en de opslaghardware controleren. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Problemen met bestanden oplossen die niet kunnen worden ingeroepen  
Als bestanden niet kunnen worden ingeroepen:
1. Bekijk Logboeken telemetrie, operationele en diagnostische gebeurtenislogboeken onder Toepassingen en services\Microsoft\FileSync\Agent.
    1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.
    2. Controleer of de server verbinding heeft met internet. 
    3. Open de MMC-module Services en controleer of de Storage Sync Agent-service (FileSyncSvc) wordt uitgevoerd.
    4. Controleer of de Azure File Sync filters (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
        - Voer uit bij een opdrachtprompt met verhoogde `fltmc` opdracht. Controleer of de StorageSync.sys en StorageSyncGuard.sys voor bestandssysteemfilters worden weergegeven.

> [!NOTE]
> Een gebeurtenis-id 9006 wordt eenmaal per uur geregistreerd in het telemetriegebeurtenislogboek als een bestand niet kan worden ingeroepen (er wordt één gebeurtenis geregistreerd per foutcode). Controleer de [sectie Fouten en herstel](#recall-errors-and-remediation) terughalen om te zien of herstelstappen worden vermeld voor de foutcode.

### <a name="recall-errors-and-remediation"></a>Fouten en herstel terughalen

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Het bestand kan het niet inroepen vanwege een I/O-time-out. Dit probleem kan verschillende oorzaken hebben: beperkingen van serverresources, een slechte netwerkverbinding of een probleem met Azure Storage (bijvoorbeeld beperking). | U hoeft geen actie te ondernemen. Als de fout gedurende enkele uren blijft bestaan, opent u een ondersteuningscase. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Het bestand kan niet worden ingeroepen vanwege een netwerkprobleem.  | Als de fout zich blijft voordoen, controleert u de netwerkverbinding met de Azure-bestands share. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Het bestand kan niet worden ingetrokken omdat het server-eindpunt is verwijderd. | Zie Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een [server-eindpunt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand kan niet worden ingeroepen vanwege een fout met geweigerde toegang. Dit probleem kan optreden als de instellingen voor de firewall en het virtuele netwerk in het opslagaccount zijn ingeschakeld en de server geen toegang heeft tot het opslagaccount. | U kunt dit probleem oplossen door het IP-adres van de server of het virtuele netwerk toe te voegen door de stappen te volgen die worden beschreven in de sectie [Firewall-](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) en virtuele netwerkinstellingen configureren in de implementatiehandleiding. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Het bestand kan niet worden ingeroepen omdat het niet toegankelijk is in de Azure-bestands share. | U kunt dit probleem oplossen door te controleren of het bestand zich in de Azure-bestands share bevindt. Als het bestand in de Azure-bestands share bestaat, upgradet u naar de meest recente Azure File Sync [agentversie](./storage-files-release-notes.md#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Het bestand kan niet worden ingeroepen vanwege een autorisatiefout in het opslagaccount. | U kunt dit probleem oplossen door te [controleren Azure File Sync toegang heeft tot het opslagaccount](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Het bestand kan niet worden ingeroepen omdat de Azure-bestands share niet toegankelijk is. | Controleer of de bestands share bestaat en toegankelijk is. Als de bestands share is verwijderd en opnieuw [](?tabs=portal1%252cazure-portal#-2134375810) is gemaakt, voert u de stappen uit die worden beschreven in Sync is mislukt omdat de Azure-bestands share is verwijderd en opnieuw is gemaakt om de synchronisatiegroep te verwijderen en opnieuw te maken. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand kan niet worden ingeroepen vanwege onvoldoende systeembronnen. | Als de fout zich blijft voordoen, moet u onderzoeken welk toepassings- of kernelmodus stuurprogramma systeembronnen uitput. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Het bestand kan niet worden ingeroepen vanwege onvoldoende geheugen. | Als de fout zich blijft voordoen, moet u onderzoeken welk toepassings- of kernelmodus stuurprogramma de oorzaak is van de situatie met weinig geheugen. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand kan niet worden ingeroepen vanwege onvoldoende schijfruimte. | U kunt dit probleem oplossen door ruimte vrij te maken op het volume door bestanden naar een ander volume te verplaatsen, de grootte van het volume te vergroten of bestanden geforceerd in een laag op te slaan met behulp van de Invoke-StorageSyncCloudTiering cmdlet. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt
Gelaagde bestanden op een server worden ontoegankelijk als de bestanden niet worden ingeroepen voordat een server-eindpunt wordt verwijderd.

Fouten die worden geregistreerd als gelaagde bestanden niet toegankelijk zijn
- Bij het synchroniseren van een bestand wordt foutcode -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) vastgelegd in het gebeurtenislogboek ItemResults
- Bij het terugroepen van een bestand wordt foutcode -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) vastgelegd in het gebeurtenislogboek RecallResults

Toegang herstellen tot uw gelaagde bestanden is mogelijk als aan de volgende voorwaarden is voldaan:
- Het servereindpunt is verwijderd in de afgelopen 30 dagen
- Het cloudeindpunt is niet verwijderd 
- De bestandsshare is niet verwijderd
- De synchronisatiegroep is niet verwijderd

Als aan de bovenstaande voorwaarden is voldaan, kunt u de toegang tot de bestanden op de server herstellen door binnen 30 dagen het servereindpunt opnieuw te maken op hetzelfde serverpad binnen dezelfde synchronisatiegroep. 

Als niet aan de bovenstaande voorwaarden is voldaan, is het niet mogelijk om de toegang te herstellen, aangezien deze gelaagde bestanden op de server nu zwevend zijn. Volg de onderstaande instructies om de zwevende gelaagde bestanden te verwijderen.

**Opmerkingen**
- Wanneer gelaagde bestanden niet toegankelijk zijn op de server, moet het volledige bestand nog steeds toegankelijk zijn als u rechtstreeks toegang hebt tot de Azure-bestands share.
- Als u zwevende gelaagde bestanden in de toekomst wilt voorkomen, volgt u de stappen die worden beschreven in [Een server-eindpunt](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) verwijderen bij het verwijderen van een server-eindpunt.

<a id="get-orphaned"></a>**De lijst met zwevende gelaagde bestanden op te halen** 

1. Controleer Azure File Sync agentversie v5.1 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sla het OrphanTieredFiles.txt op voor het geval bestanden moeten worden hersteld vanuit een back-up nadat ze zijn verwijderd.

<a id="remove-orphaned"></a>**Zwevende gelaagde bestanden verwijderen** 

*Optie 1: verwijder de zwevende gelaagde bestanden*

Met deze optie worden de zwevende gelaagde bestanden op de Windows-server verwijderd, maar moet het server-eindpunt worden verwijderd als dit bestaat vanwege een probleem na 30 dagen of is verbonden met een andere synchronisatiegroep. Bestandsconflicten treden op als bestanden worden bijgewerkt op de Windows Server- of Azure-bestands share voordat het server-eindpunt opnieuw wordt gemaakt.

1. Controleer Azure File Sync agentversie v5.1 of hoger is geïnstalleerd.
2. Back-up maken van de Azure-bestands share en de locatie van het server-eindpunt.
3. Verwijder het server-eindpunt in de synchronisatiegroep (indien aanwezig) door de stappen te volgen die worden beschreven in [Een server-eindpunt verwijderen.](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)

> [!Warning]  
> Als het server-eindpunt niet wordt verwijderd voordat de Remove-StorageSyncOrphanedTieredFiles-cmdlet wordt gebruikt, wordt het volledige bestand in de Azure-bestands share verwijderd als u het zwevende gelaagde bestand op de server verwijdert. 

4. Voer de volgende PowerShell-opdrachten uit om zwevende gelaagde bestanden weer te geven:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Sla het OrphanTieredFiles.txt op voor het geval bestanden moeten worden hersteld vanuit een back-up nadat ze zijn verwijderd.
6. Voer de volgende PowerShell-opdrachten uit om zwevende gelaagde bestanden te verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Opmerkingen** 
- Gelaagde bestanden die zijn gewijzigd op de server die niet zijn gesynchroniseerd met de Azure-bestands share, worden verwijderd.
- Gelaagde bestanden die toegankelijk zijn (niet zwevend) worden niet verwijderd.
- Niet-gelaagde bestanden blijven op de server.

7. Optioneel: maak het server-eindpunt opnieuw als het in stap 3 is verwijderd.

*Optie 2: de Azure-bestands share mounten en de bestanden lokaal kopiëren die zwevend zijn op de server*

Voor deze optie hoeft het server-eindpunt niet te worden verwijderd, maar is voldoende schijfruimte vereist om de volledige bestanden lokaal te kopiëren.

1. [De](./storage-how-to-use-files-windows.md) Azure-bestands share met zwevende gelaagde bestanden aan de Windows-server toevoegen.
2. Voer de volgende PowerShell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Gebruik het OrphanTieredFiles.txt uitvoerbestand om zwevende gelaagde bestanden op de server te identificeren.
4. Overschrijf de zwevende gelaagde bestanden door het volledige bestand van de Azure-bestands share naar windows Server te kopiëren.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Problemen met bestanden die onverwacht worden ingeroepen op een server oplossen  
Antivirusprogramma's, back-ups en andere toepassingen die grote aantallen bestanden lezen, veroorzaken onbedoelde terugroepen, tenzij ze het kenmerk offline overslaan respecteren en het lezen van de inhoud van die bestanden overslaan. Als u offlinebestanden overslaat voor producten die ondersteuning bieden voor deze optie, voorkomt u dat er onbedoeld wordt teruggehaald tijdens bijvoorbeeld antivirusscans en back-uptaken.

Vraag uw softwareleverancier hoe de oplossing kan worden geconfigureerd zodat offlinebestanden niet meer worden gelezen.

Onbedoelde terugroepen kunnen ook optreden in andere scenario's, zoals wanneer u bestanden bladert in Verkenner. Als u in Verkenner op de server een map opent met bestanden in cloudlagen, kan dat resulteren in onbedoeld terughalen. Dit is nog waarschijnlijker als er een antivirusoplossing is ingeschakeld op de server.

> [!NOTE]
>Gebruik gebeurtenis-id 9059 in het telemetriegebeurtenislogboek om te bepalen welke toepassing(en) de terugroepen veroorzaken. Deze gebeurtenis biedt distributie van terugroepen van toepassingen voor een server-eindpunt en wordt eenmaal per uur geregistreerd.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 vereist voor Azure File Sync

U kunt de TLS-instellingen op uw server bekijken door de [registerinstellingen te bekijken.](/windows-server/security/tls/tls-registry-settings) 

Als u een proxy gebruikt, raadpleegt u de documentatie van uw proxy en zorgt u ervoor dat deze is geconfigureerd voor het gebruik van TLS1.2.

## <a name="general-troubleshooting"></a>Algemene probleemoplossing
Als u problemen ondervindt met Azure File Sync server, begint u met het uitvoeren van de volgende stappen:
1. Bekijk Logboeken logboeken met telemetriegebeurtenissen, operationele en diagnostische gebeurtenissen.
    - Synchronisatie-, opslag- en terugroepproblemen worden vastgelegd in de telemetrie-, diagnostische en operationele gebeurtenislogboeken onder Applications and Services\Microsoft\FileSync\Agent.
    - Problemen met betrekking tot het beheren van een server (bijvoorbeeld configuratie-instellingen) worden vastgelegd in de logboeken met operationele en diagnostische gebeurtenissen onder Applications and Services\Microsoft\FileSync\Management.
2. Controleer of Azure File Sync service wordt uitgevoerd op de server:
    - Open de MMC-module Services en controleer of de Service opslagsynchronisatieagent (FileSyncSvc) wordt uitgevoerd.
3. Controleer of de Azure File Sync filters (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
    - Voer uit bij een opdrachtprompt met verhoogde `fltmc` opdracht. Controleer of de StorageSync.sys en StorageSyncGuard.sys voor bestandssysteemfilters worden weergegeven.

Als het probleem niet is opgelost, voer dan het AFSDiag-hulpprogramma uit en verzend de uitvoer van het ZIP-bestand naar de ondersteuningstechnicus die aan uw case is toegewezen voor verdere diagnose.

Voer de onderstaande stappen uit om AFSDiag uit te voeren.

Voor agentversie v11 en hoger:
1. Open een PowerShell-venster met verhoogde bevoegdheid en voer de volgende opdrachten uit (druk na elke opdracht op Enter):

    > [!NOTE]
    >AFSDiag maakt de uitvoermap en een tijdelijke map in de map vóór het verzamelen van logboeken en verwijdert de tijdelijke map na de uitvoering. Geef een uitvoerlocatie op die geen gegevens bevat.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reproduceer het probleem. Wanneer u klaar bent, voert u **D in.**
3. Een ZIP-bestand dat logboeken en traceerbestanden bevat, wordt opgeslagen in de uitvoermap die u hebt opgegeven. 

Voor agentversie v10 en eerder:
1. Maak een map waarin de AFSDiag-uitvoer wordt opgeslagen (bijvoorbeeld C:\Output).
    > [!NOTE]
    >AFSDiag verwijdert alle inhoud in de uitvoermap voordat logboeken worden verzameld. Geef een uitvoerlocatie op die geen gegevens bevat.
2. Open een PowerShell-venster met verhoogde bevoegdheid en voer de volgende opdrachten uit (druk na elke opdracht op Enter):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Voor het Azure File Sync kernelmodus voert u **1** in (tenzij anders aangegeven, om uitgebreidere traceringen te maken) en drukt u op Enter.
4. Voer voor Azure File Sync traceerniveau van de gebruikersmodus **1** in (tenzij anders aangegeven, om uitgebreidere traceringen te maken) en druk vervolgens op Enter.
5. Reproduceer het probleem. Wanneer u klaar bent, voert u **D in.**
6. Een ZIP-bestand dat logboeken en traceerbestanden bevat, wordt opgeslagen in de uitvoermap die u hebt opgegeven.


## <a name="see-also"></a>Zie ook
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Azure Files veelgestelde vragen](storage-files-faq.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen Azure Files linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
