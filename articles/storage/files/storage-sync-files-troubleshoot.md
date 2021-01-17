---
title: Problemen oplossen Azure File Sync | Microsoft Docs
description: Los veelvoorkomende problemen in een implementatie op Azure File Sync op, die u kunt gebruiken om Windows Server te transformeren naar een snelle cache van uw Azure-bestands share.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 1/15/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 71de1d17731e086d012da5365fa6671bcb9e6e3b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539241"
---
# <a name="troubleshoot-azure-file-sync"></a>Problemen met Azure Files Sync oplossen
Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

Dit artikel is bedoeld om u te helpen bij het oplossen van problemen die kunnen optreden met uw Azure File Sync-implementatie. We beschrijven ook hoe belang rijke logboeken van het systeem moeten worden verzameld als een grondig onderzoek van het probleem is vereist. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

1. [Micro soft Q&een vraag pagina voor Azure Storage](/answers/products/azure?product=storage).
2. [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Ondersteuning. Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het Azure Portal op het tabblad **Help** de knop **Help en ondersteuning** en selecteert u vervolgens **nieuwe ondersteunings aanvraag**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, Cloud lagen, enz.). Moet ik mijn server-eind punt verwijderen en opnieuw maken?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agent-installatie en Server registratie
<a id="agent-installation-failures"></a>**Problemen met installatie fouten van agent oplossen**  
Als de installatie van de Azure File Sync agent mislukt, voert u bij een opdracht prompt met verhoogde bevoegdheid de volgende opdracht uit om logboek registratie in te scha kelen tijdens de installatie van de agent:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Controleer de installatie van Installer. log om de oorzaak van de installatie fout te bepalen.

<a id="agent-installation-on-DC"></a>**Agent installatie mislukt op Active Directory-domein controller**  
Als u probeert de synchronisatie agent te installeren op een Active Directory domein controller waar de eigenaar van de PDC-functie zich bevindt op een Windows Server 2008 R2 of onder de versie van het besturings systeem, kunt u het probleem bereiken waarbij de synchronisatie agent niet kan worden geïnstalleerd.

U kunt dit oplossen door de PDC-functie over te dragen naar een andere domein controller met Windows Server 2012 R2 of meer recent en vervolgens de synchronisatie te installeren.

<a id="parameter-is-incorrect"></a>**Het is niet mogelijk om toegang te krijgen tot een volume op Windows Server 2012 R2 met de volgende fout: de para meter is onjuist**  
Na het maken van een server eindpunt op Windows Server 2012 R2, treedt de volgende fout op bij het openen van het volume:

stationsletter: \ is niet toegankelijk.  
De parameter is onjuist.

U kunt dit probleem oplossen door [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) te installeren en de server opnieuw op te starten. Als deze update niet kan worden geïnstalleerd omdat een latere update al is geïnstalleerd, gaat u naar Windows Update, installeert u de nieuwste updates voor Windows Server 2012 R2 en start u de server opnieuw op.

<a id="server-registration-missing-subscriptions"></a>**Bij de server registratie worden niet alle Azure-abonnementen weer geven**  
Wanneer u een server registreert met behulp van ServerRegistration.exe, ontbreken er abonnementen wanneer u op de vervolg keuzelijst van het Azure-abonnement klikt.

Dit probleem treedt op omdat met ServerRegistration.exe alleen abonnementen worden opgehaald van de eerste 5 Azure AD-tenants. 

Als u de Tenant limiet voor de server registratie op de server wilt verhogen, maakt u een DWORD-waarde met de naam ServerRegistrationTenantLimit onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync met een waarde die groter is dan 5.

U kunt dit probleem ook verhelpen met behulp van de volgende Power shell-opdrachten om de server te registreren:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Bij de registratie van de server wordt het volgende bericht weer gegeven: "er ontbreken vereisten"**  
Dit bericht wordt weer gegeven als AZ of AzureRM Power shell-module niet is geïnstalleerd in Power shell 5,1. 

> [!Note]  
> ServerRegistration.exe biedt geen ondersteuning voor Power shell 6. x. U kunt de cmdlet Register-AzStorageSyncServer op Power shell 6. x gebruiken om de server te registreren.

Voer de volgende stappen uit om de module AZ of AzureRM te installeren op Power shell 5,1:

1. Typ **Power shell** vanaf een opdracht prompt met verhoogde bevoegdheid en druk op ENTER.
2. Installeer de nieuwste AZ-of AzureRM-module door de documentatie te volgen:
    - [AZ-module (hiervoor is .NET 4.7.2 vereist)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [AzureRM-module]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren bij een opslag synchronisatie service.

<a id="server-already-registered"></a>**Bij de server registratie wordt het volgende bericht weer gegeven: "deze server is al geregistreerd"** 

![Een scherm afbeelding van het dialoog venster Server registratie met het fout bericht ' server is al geregistreerd '](media/storage-sync-files-troubleshoot/server-registration-1.png)

Dit bericht wordt weer gegeven als de server eerder is geregistreerd bij een opslag synchronisatie service. Als u de registratie van de server bij de huidige opslag synchronisatie service ongedaan wilt maken en vervolgens wilt registreren bij een nieuwe opslag synchronisatie service, voert u de stappen uit die worden beschreven in de [registratie van een server bij Azure file sync ongedaan](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)maken.

Als de server niet wordt vermeld onder **geregistreerde servers** in de opslag synchronisatie service, voert u de volgende Power shell-opdrachten uit op de server die u wilt verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Als de server deel uitmaakt van een cluster, kunt u de optionele para meter *Reset-StorageSyncServer-CleanClusterRegistration* gebruiken om ook de cluster registratie te verwijderen.

<a id="web-site-not-trusted"></a>**Wanneer ik een server registreer, zie ik talloze ' website-niet-vertrouwde ' antwoorden. Waarom?**  
Dit probleem treedt op wanneer het **Verbeterde beveiligings beleid van Internet Explorer** is ingeschakeld tijdens de registratie van de server. Voor meer informatie over het correct uitschakelen van het **Verbeterde beveiligings beleid voor Internet Explorer** , Zie [Windows Server voorbereiden voor gebruik met Azure file sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) en [hoe u Azure file sync implementeert](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**De server wordt niet vermeld onder geregistreerde servers in de Azure Portal**  
Als een server niet wordt vermeld onder **geregistreerde servers** voor een opslag synchronisatie service:
1. Meld u aan bij de server die u wilt registreren.
2. Open bestanden Verkenner en ga naar de installatiemap van de opslag synchronisatie agent (de standaard locatie is C:\Program Files\Azure\StorageSyncAgent). 
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren bij een opslag synchronisatie service.

## <a name="sync-group-management"></a>Groeps beheer synchroniseren

### <a name="cloud-endpoint-creation-errors"></a>Fouten bij het maken van Cloud-eind punten

<a id="cloud-endpoint-using-share"></a>**Het maken van een Cloud-eind punt is mislukt, met de volgende fout: ' de opgegeven Azure-bestands share wordt al gebruikt door een andere CloudEndpoint '**  
Deze fout treedt op wanneer de Azure-bestandsshare al wordt gebruikt door een ander cloudeindpunt. 

Als u dit bericht ziet en de Azure-bestands share momenteel niet wordt gebruikt door een Cloud eindpunt, voert u de volgende stappen uit om de Azure File Sync meta gegevens op de Azure-bestands share te wissen:

> [!Warning]  
> Het verwijderen van de meta gegevens van een Azure-bestands share die momenteel wordt gebruikt door een Cloud-eind punt zorgt ervoor dat Azure File Sync bewerkingen mislukken. 

1. Ga in het Azure Portal naar uw Azure-bestands share.  
2. Klik met de rechter muisknop op de Azure-bestands share en selecteer vervolgens **meta gegevens bewerken**.
3. Klik met de rechter muisknop op **SyncService** en selecteer **verwijderen**.

<a id="cloud-endpoint-authfailed"></a>**Het maken van een Cloud-eind punt is mislukt, met de volgende fout: ' AuthorizationFailed '**  
Deze fout treedt op als uw gebruikers account niet voldoende rechten heeft voor het maken van een Cloud eindpunt. 

Als u een Cloud eindpunt wilt maken, moet uw gebruikers account over de volgende micro soft-autorisatie machtigingen beschikken:  
* Lezen: roldefinitie ophalen
* Schrijven: aangepaste roldefinitie maken of bijwerken
* Lezen: roltoewijzing ophalen
* Schrijven: roltoewijzing maken

De volgende ingebouwde rollen hebben de vereiste micro soft-autorisatie machtigingen:  
* Eigenaar
* Beheerder van gebruikerstoegang

Om te bepalen of de rol van uw gebruikers account over de vereiste machtigingen beschikt:  
1. Selecteer **resource groepen** In het Azure Portal.
2. Selecteer de resource groep waar het opslag account zich bevindt en selecteer vervolgens **toegangs beheer (IAM)**.
3. Selecteer het tabblad **roltoewijzingen** .
4. Selecteer de **rol** (bijvoorbeeld eigenaar of Inzender) voor uw gebruikers account.
5. Selecteer in de lijst **resource provider** de optie **micro soft-autorisatie**. 
    * De **roltoewijzing** moet **Lees** -en **Schrijf** machtigingen hebben.
    * De **roldefinitie** moet **Lees** -en **Schrijf** machtigingen hebben.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Fouten bij het maken en verwijderen van het server eindpunt

<a id="-2134375898"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134375898 of 0x80c80226)**  
Deze fout treedt op als het pad naar het servereindpunt zich op het systeemvolume bevindt en opslag in cloudlagen is ingeschakeld. Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.

<a id="-2147024894"></a>**Het maken van het server eindpunt is mislukt met de volgende fout: "MgmtServerJobFailed" (fout code:-2147024894 of 0x80070002)**  
Deze fout treedt op als het opgegeven pad naar het servereindpunt niet geldig is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Opmerking: Azure File Sync biedt geen ondersteuning voor toegewezen stations als een pad naar een server eindpunt.

<a id="-2134375640"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134375640 of 0x80c80328)**  
Deze fout treedt op als het opgegeven pad naar het server eindpunt geen NTFS-volume is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Opmerking: Azure File Sync biedt geen ondersteuning voor toegewezen stations als een pad naar een server eindpunt.

<a id="-2134347507"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134347507 of 0x80c8710d)**  
Deze fout treedt op omdat Azure File Sync geen servereindpunten ondersteunt op volumes die een gecomprimeerde map met informatie over systeemvolumes hebben. Om dit probleem op te lossen, decomprimeert u de map met informatie over het systeemvolume. Als de map met informatie over het systeemvolume de enige map is die op het volume is gecomprimeerd, voert u de volgende stappen uit:

1. Down load het [PsExec](/sysinternals/downloads/psexec) -hulp programma.
2. Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om een opdracht prompt te starten die wordt uitgevoerd onder het systeem account: **PsExec.exe-i-s-d-cmd**
3. Typ in de opdrachtprompt die onder het systeemaccount wordt uitgevoerd, de volgende commando's in en druk op enter:   
    **cd/d "stationsletter: \ System Volume Information"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134376345 of 0x80C80067)**  
Deze fout treedt op als de limiet voor het aantal servereindpunten per server is bereikt. Azure File Sync ondersteunt momenteel maximaal 30 servereindpunten per server. Zie [Azure file sync Scale-doelen](./storage-files-scale-targets.md#azure-file-sync-scale-targets)voor meer informatie.

<a id="-2134376427"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134376427 of 0x80c80015)**  
Deze fout treedt op als er al een ander servereindpunt wordt gesynchroniseerd met het opgegeven pad naar het servereindpunt. Azure File Sync biedt geen ondersteuning voor meerdere servereindpunten die dezelfde map of hetzelfde volume synchroniseren.

<a id="-2160590967"></a>**Het maken van een server eindpunt mislukt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2160590967 of 0x80c80077)**  
Deze fout treedt op als het pad naar het server eindpunt zwevende gelaagde bestanden bevat. Als een server-eind punt onlangs is verwijderd, wacht u totdat het opruimen van de zwevende gelaagde bestanden is voltooid. Gebeurtenis-ID 6662 wordt vastgelegd in het telemetrie-gebeurtenis logboek wanneer het opruimen van de zwevende gelaagde bestanden is gestart. Gebeurtenis-ID 6661 wordt vastgelegd zodra het opruimen van de zwevende gelaagde bestanden is voltooid en een server eindpunt opnieuw kan worden gemaakt met behulp van het pad. Als het server-eind punt niet kan worden gemaakt nadat de opschoning van de gelaagde bestanden is voltooid of als gebeurtenis-ID 6661 niet kan worden gevonden in het telemetrie-gebeurtenis logboek als gevolg van een rollover van het gebeurtenis logboek, verwijdert u de zwevende gelaagde bestanden door de stappen uit te voeren die worden beschreven in de [gelaagde bestanden niet toegankelijk zijn op de](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**Het verwijderen van het server eindpunt is mislukt, met de volgende fout: "MgmtServerJobExpired" (fout code:-2134347757 of 0x80c87013)**  
Deze fout treedt op als de server offline is of geen netwerkverbinding heeft. Als de server niet meer beschikbaar is, moet u de registratie van de server in het portaal opheffen, waardoor de servereindpunten worden verwijderd. Als u de server eindpunten wilt verwijderen, volgt u de stappen die worden beschreven in de [registratie van een server bij Azure file sync ongedaan maken](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Status van server eindpunt

<a id="server-endpoint-provisioningfailed"></a>**Kan de eigenschappen pagina van het server eindpunt niet openen of het beleid voor Cloud lagen niet bijwerken**  
Dit probleem kan optreden als een beheer bewerking op het server eindpunt mislukt. Als de pagina eindpunt eigenschappen van de server niet wordt geopend in de Azure Portal, kan het server eindpunt bijwerken met behulp van Power shell-opdrachten van de server dit probleem mogelijk oplossen. 

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
<a id="server-endpoint-noactivity"></a>**Server-eind punt heeft de status ' geen activiteit ' of ' in behandeling ' en de status van de server op de Blade geregistreerde servers is ' offline weer gegeven '**  

Dit probleem kan optreden als het proces voor het controleren van de opslag synchronisatie (AzureStorageSyncMonitor.exe) niet wordt uitgevoerd of als de server geen toegang heeft tot de Azure File Sync-Service.

Op de server die wordt weer gegeven als ' offline weer geven ' in de portal, kijkt u naar gebeurtenis-ID 9301 in het telemetrie-gebeurtenis logboek (onder toepassingen en Services\Microsoft\FileSync\Agent in Logboeken) om te bepalen waarom de server geen toegang heeft tot de Azure File Sync-Service. 

- Als **GetNextJob is voltooid met de status: 0** is geregistreerd, kan de server met de Azure File Sync-Service communiceren. 
    - Open Taakbeheer op de server en controleer of het Storage Sync Monitor-proces (AzureStorageSyncMonitor.exe) actief is. Als het proces niet wordt uitgevoerd, start u om te beginnen de server opnieuw op. Als het probleem niet wordt verholpen bij het opnieuw opstarten van de server, voert u een upgrade naar de laatste versie van de Azure File Sync-[agent](./storage-files-release-notes.md) uit. 

- Als **GetNextJob is voltooid met status:-2134347756** wordt geregistreerd, kan de server niet communiceren met de Azure file sync-service vanwege een firewall-, proxy-of TLS-coderings Suite. 
    - Als de server zich achter een firewall bevindt, controleert u of uitgaand verkeer via poort 443 is toegestaan. Als de firewall het verkeer naar specifieke domeinen beperkt, moet u controleren of de domeinen die worden vermeld in de firewall [documentatie](./storage-sync-files-firewall-and-proxy.md#firewall) toegankelijk zijn.
    - Als de-server zich achter een proxy bevindt, configureert u de computer-of app-specifieke proxy-instellingen door de stappen in de proxy- [documentatie](./storage-sync-files-firewall-and-proxy.md#proxy)te volgen.
    - Gebruik de cmdlet Test-StorageSyncNetworkConnectivity om de netwerk verbinding met de service-eind punten te controleren. Zie [netwerk connectiviteit testen op service-eind punten](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)voor meer informatie.
    - Als de volg orde van de TLS-coderings Suite op de server is geconfigureerd, kunt u groeps beleid of TLS-cmdlets gebruiken om coderings suites toe te voegen:
        - Zie de [volg orde van TLS-coderings pakketten configureren met behulp van Groepsbeleid](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)om groeps beleid te gebruiken.
        - Zie [Configure TLS cipher Suite order by using TLS Power shell cmdlets](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)(Engelstalig) voor het gebruik van TLS-cmdlets.
    
        Azure File Sync ondersteunt momenteel de volgende coderings suites voor TLS 1,2-Protocol:  
        - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256  
        - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256  
        - TLS_RSA_WITH_AES_256_GCM_SHA384  
        - TLS_RSA_WITH_AES_128_GCM_SHA256  
        - TLS_RSA_WITH_AES_256_CBC_SHA256  
        - TLS_RSA_WITH_AES_128_CBC_SHA256  

- Als **GetNextJob is voltooid met de status:-2134347764** wordt geregistreerd, kan de server niet communiceren met de Azure file sync-service vanwege een verlopen of verwijderd certificaat.  
    - Voer de volgende Power shell-opdracht op de server uit om het certificaat dat voor verificatie wordt gebruikt, opnieuw in te stellen:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Server-eind punt heeft de status ' geen activiteit ' en de status van de server op de Blade geregistreerde servers is ' online '**  

Als een servereindpunt de status 'Geen activiteit' heeft, betekent dit dat het servereindpunt de afgelopen twee uur geen synchronisatieactiviteit heeft gelogd.

Zie [Hoe kan ik de voortgang van een huidige synchronisatie sessie controleren](#how-do-i-monitor-the-progress-of-a-current-sync-session)als u de huidige synchronisatie activiteit op een server wilt controleren.

Een server eindpunt kan de synchronisatie activiteit niet registreren gedurende enkele uren vanwege een fout of onvoldoende systeem resources. Controleer of de meest recente versie van Azure File Sync [agent](./storage-files-release-notes.md) is geïnstalleerd. Als het probleem zich blijft voordoen, opent u een ondersteunings aanvraag.

> [!Note]  
> Als de status van de server op de Blade geregistreerde servers ' offline weer gegeven ' is, voert u de stappen uit die worden beschreven in het [Server-eind punt de status ' geen activiteit ' of ' in behandeling ' en de status van de server op de Blade geregistreerde servers is ' offline weer gegeven '](#server-endpoint-noactivity) .

## <a name="sync"></a>Synchroniseren
<a id="afs-change-detection"></a>**Hoe lang duurt het om het bestand te synchroniseren met servers in de synchronisatie groep als ik een bestand rechtstreeks in mijn Azure-bestands share heb gemaakt via SMB of via de portal?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**De status van het server eindpunt is gedurende enkele uren nog in behandeling**  
Dit probleem wordt verwacht als u een Cloud-eind punt maakt en een Azure-bestands share gebruikt die gegevens bevat. De wijzigings inventarisatie taak waarmee wordt gescand op wijzigingen in de Azure-bestands share moet zijn voltooid voordat bestanden kunnen worden gesynchroniseerd tussen de Cloud en server eindpunten. De tijd voor het volt ooien van de taak is afhankelijk van de grootte van de naam ruimte in de Azure-bestands share. De status van het server eindpunt moet worden bijgewerkt zodra de inventarisatie taak is gewijzigd.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Synchronisatie status Hoe kan ik controleren?
# <a name="portal"></a>[Portal](#tab/portal1)
Binnen elke synchronisatie groep kunt u inzoomen op de afzonderlijke server eindpunten om de status van de laatst voltooide synchronisatie sessies weer te geven. Een groene status kolom en een bestand met een niet-gesynchroniseerde waarde van 0 geven aan dat de synchronisatie werkt zoals verwacht. Als dit niet het geval is, raadpleegt u hieronder voor een lijst met algemene synchronisatie fouten en het afhandelen van bestanden die niet worden gesynchroniseerd. 

![Een scherm opname van de Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Ga naar de telemetrie-logboeken van de server, die u kunt vinden in de Logboeken op `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Gebeurtenis 9102 komt overeen met een voltooide synchronisatie sessie. Zoek naar de meest recente gebeurtenis met ID 9102 voor de laatste synchronisatie status. SyncDirection vertelt u of deze sessie een upload-of download bewerking is. Als HResult 0 is, is de synchronisatie sessie geslaagd. Een niet-nul HResult houdt in dat er een fout is opgetreden tijdens de synchronisatie. Hieronder vindt u een lijst met veelvoorkomende fouten. Als de PerItemErrorCount groter is dan 0, betekent dit dat sommige bestanden of mappen niet juist zijn gesynchroniseerd. Het is mogelijk een HResult van 0 te hebben, maar een PerItemErrorCount die groter is dan 0.

Hieronder ziet u een voor beeld van een geslaagde upload. Voor het omwille van de boogën worden slechts enkele van de waarden in elke 9102-gebeurtenis hieronder weer gegeven. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Een mislukte upload kan er echter als volgt uitzien:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Soms mislukken synchronisatie sessies volledig of hebben ze een PerItemErrorCount die niet gelijk zijn aan nul, maar worden er nog steeds een andere bewerking uitgevoerd, waarbij sommige bestanden met succes worden gesynchroniseerd. Dit kan worden weer gegeven in de velden toegepast * (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes), waarin u kunt zien hoeveel van de sessie slaagt. Als er meerdere synchronisatie sessies in een rij worden weer geven die niet werken, maar wel een oplopend toegepast aantal hebben, moet u de synchronisatie tijd opgeven om het opnieuw te proberen voordat een ondersteunings ticket wordt geopend.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hoe controleer ik de voortgang van een synchronisatiesessie?
# <a name="portal"></a>[Portal](#tab/portal1)
Ga in de synchronisatie groep naar het server eindpunt in kwestie en Bekijk de sectie synchronisatie activiteit om het aantal bestanden te zien dat in de huidige synchronisatie sessie is geüpload of gedownload. Houd er rekening mee dat deze status ongeveer 5 minuten achterloopt, en dat als uw synchronisatiesessie klein genoeg is om binnen deze periode te worden voltooid, deze mogelijk niet in de portal wordt gerapporteerd. 

# <a name="server"></a>[Server](#tab/server)
Bekijk de meest recente 9302-gebeurtenis in het logboek van de telemetrie op de server (Ga in het Logboeken naar toepassingen en services Logs\Microsoft\FileSync\Agent\Telemetry). Deze gebeurtenis geeft de status van de huidige synchronisatiesessie aan. TotalItemCount geeft aan hoeveel bestanden moeten worden gesynchroniseerd, AppliedItemCount het aantal bestanden dat tot nu toe is gesynchroniseerd en PerItemErrorCount het aantal bestanden dat niet kan worden gesynchroniseerd (zie hieronder voor meer informatie over hoe u dit kunt doen).

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
Voor elke server in een bepaalde synchronisatie groep moet u het volgende doen:
- De tijds tempels voor de laatste synchronisatie poging voor zowel uploaden als downloaden zijn recent.
- De status is groen voor uploaden en downloaden.
- In het veld synchronisatie activiteit worden weinig of geen bestanden weer gegeven om te synchroniseren.
- Het veld bestanden niet synchroniseren is 0 voor uploaden en downloaden.

# <a name="server"></a>[Server](#tab/server)
Bekijk de voltooide synchronisatie sessies, die zijn gemarkeerd door 9102-gebeurtenissen in het logboek voor telemetrie voor elke server (in de Logboeken, gaat u naar `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Op elke wille keurige server moet u ervoor zorgen dat de nieuwste upload-en download sessies zijn voltooid. Om dit te doen, controleert u of HResult en PerItemErrorCount 0 zijn voor uploaden en downloaden (het veld SyncDirection geeft aan of een bepaalde sessie een upload-of download sessie is). Houd er rekening mee dat als u geen recent voltooide synchronisatie sessie ziet, er waarschijnlijk een synchronisatie sessie wordt uitgevoerd. dit wordt verwacht als u een grote hoeveelheid gegevens hebt toegevoegd of gewijzigd.
2. Wanneer een server volledig up-to-date is met de Cloud en er geen wijzigingen in een van beide richtingen worden gesynchroniseerd, worden er lege synchronisatie sessies weer geven. Deze worden aangegeven door upload-en download gebeurtenissen waarin alle Sync *-velden (SyncFileCount, SyncDirCount, SyncTombstoneCount en SyncSizeBytes) nul zijn, wat betekent dat er niets kan worden gesynchroniseerd. Houd er rekening mee dat deze lege synchronisatie sessies mogelijk niet worden uitgevoerd op servers met een hoge verloop tijd omdat er altijd iets nieuws is om te synchroniseren. Als er geen synchronisatie activiteit is, moeten deze elke 30 minuten worden uitgevoerd. 
3. Als alle servers up-to-date zijn met de Cloud, wat betekent dat de recente upload-en download sessies lege synchronisatie sessies zijn, kunt u met redelijke zekerheid zeggen dat het systeem als geheel is gesynchroniseerd. 
    
Houd er rekening mee dat als u wijzigingen rechtstreeks in uw Azure-bestands share hebt aangebracht, deze wijziging pas door Azure File Sync wordt gedetecteerd nadat de inventarisatie wordt uitgevoerd, die elke 24 uur plaatsvindt. Het is mogelijk dat een server het is bijgewerkt met de Cloud wanneer de recente wijzigingen die rechtstreeks in de Azure-bestands share zijn aangebracht, ontbreken. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hoe zie ik of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?
Als uw PerItemErrorCount op de server of bestanden die niet zijn gesynchroniseerd in de portal groter zijn dan 0 voor een bepaalde synchronisatie sessie, betekent dit dat sommige items niet kunnen worden gesynchroniseerd. Bestanden en mappen kunnen kenmerken hebben die voor komen dat ze worden gesynchroniseerd. Deze kenmerken kunnen permanent zijn en vereisen expliciete acties om de synchronisatie te hervatten, bijvoorbeeld het verwijderen van niet-ondersteunde tekens uit de naam van het bestand of de map. Ze kunnen ook tijdelijk zijn, wat betekent dat het bestand of de map automatisch wordt hervat. bestanden met open ingangen worden bijvoorbeeld automatisch hervat wanneer het bestand wordt gesloten. Wanneer de Azure File Sync-Engine een dergelijk probleem detecteert, wordt er een fouten logboek gegenereerd dat kan worden geparseerd om de items weer te geven die momenteel niet worden gesynchroniseerd.

Als u deze fouten wilt zien, voert u **FileSyncErrorsReport.ps1** het Power shell-script (dat zich in de installatie directory van de agent van de Azure file sync agent) bevindt uit om te bepalen welke bestanden niet kunnen worden gesynchroniseerd vanwege open ingangen, niet-ondersteunde tekens of andere problemen. Het veld het itempad vertelt u de locatie van het bestand ten opzichte van de hoofdmap voor synchronisatie. Zie de lijst met veelvoorkomende synchronisatie fouten voor herstels tappen.

> [!Note]  
> Als het FileSyncErrorsReport.ps1 script retourneert dat er geen bestands fouten zijn gevonden of er geen lijst met fouten per item voor de synchronisatie groep wordt weer gegeven, is de oorzaak ofwel:
>
>- Oorzaak 1: de laatste voltooide synchronisatie sessie heeft geen fouten per item. De portal moet binnenkort worden bijgewerkt om 0 bestanden weer te geven die niet worden gesynchroniseerd. 
>    - Controleer de [gebeurtenis-ID 9102](?tabs=server%252cazure-portal#broken-sync) in het logboek voor telemetrie om te bevestigen dat de PerItemErrorCount 0 is. 
>
>- Oorzaak 2: het ItemResults-gebeurtenis logboek op de server die is verpakt als gevolg van te veel fouten per item en het gebeurtenis logboek bevat geen fouten meer voor deze synchronisatie groep.
>    - Verhoog de grootte van het ItemResults-gebeurtenis logboek om dit probleem te voor komen. Het ItemResults-gebeurtenis logboek vindt u onder ' toepassingen en services Logs\Microsoft\FileSync\Agent ' in Logboeken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Problemen oplossen per bestand/directory-synchronisatie fouten
**ItemResults-logboek: synchronisatie fouten per item**  

| HRESULT | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Het gelaagde bestand op de server is niet toegankelijk. Dit probleem treedt op als het gelaagde bestand niet is ingetrokken voordat een servereindpunt is verwijderd. | Zie [gelaagde bestanden niet toegankelijk op de server nadat u een server eindpunt hebt verwijderd](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | De wijziging van het bestand of de map kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout zich enkele dagen voordoet, gebruikt u het Power shell-script van FileSyncErrorsReport.ps1 om te bepalen waarom de afhankelijke map nog niet is gesynchroniseerd. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | De wijziging van het bestand of de map kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout zich enkele dagen voordoet, gebruikt u het Power shell-script van FileSyncErrorsReport.ps1 om te bepalen waarom de afhankelijke map nog niet is gesynchroniseerd. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | De wijziging van het bestand of de map kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd en de synchronisatie sessie is mislukt. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, onderzoekt u de synchronisatie sessie fout. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | De naam van het bestand of de map is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie het [verwerken van niet-ondersteunde tekens](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | De naam van het bestand of de map is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie het [verwerken van niet-ondersteunde tekens](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. Azure File Sync maakt een tijdelijke VSS-moment opname eenmaal per dag op de server om bestanden met geopende ingangen te synchroniseren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Het bestand is gewijzigd, maar de wijziging is nog niet gedetecteerd door de synchronisatie. De synchronisatie wordt hersteld nadat deze wijziging is gedetecteerd. | U hoeft geen actie te ondernemen. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Het bestand is verwijderd en de synchronisatie is niet op de hoogte van de wijziging. | U hoeft geen actie te ondernemen. Deze fout wordt niet meer in de logboek registratie vastgelegd zodra de detectie van wijzigingen detecteert dat het bestand is verwijderd. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Het verwijderen van een bestand of map kan niet worden gesynchroniseerd omdat het item al is verwijderd in het doel en de wijziging niet op de hoogte is. | U hoeft geen actie te ondernemen. Deze fout wordt niet meer in de logboek registratie vastgelegd zodra de detectie van wijzigingen op de bestemming wordt uitgevoerd en de synchronisatie detecteert dat het item is verwijderd. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Het bestand of de map is overgeslagen, maar wordt gesynchroniseerd tijdens de volgende synchronisatie sessie. Als deze fout wordt gerapporteerd bij het downloaden van het item, is de naam van het bestand of de map groter dan waarschijnlijk ongeldig. | Er is geen actie vereist als deze fout wordt gerapporteerd bij het uploaden van het bestand. Als de fout wordt gerapporteerd bij het downloaden van het bestand, wijzigt u de naam van het bestand of de map in kwestie. Zie het [verwerken van niet-ondersteunde tekens](?tabs=portal1%252cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Het maken van een bestand of map kan niet worden gesynchroniseerd omdat het item al bestaat in het doel en de synchronisatie niet op de hoogte is van de wijziging. | U hoeft geen actie te ondernemen. Synchronisatie stopt deze fout wanneer de detectie van wijzigingen op de bestemming wordt uitgevoerd en de synchronisatie van dit nieuwe item op de hoogte is. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Het bestand kan niet worden gesynchroniseerd omdat de limiet voor Azure-bestandsshares is bereikt. | Zie [de sectie opslag limiet van Azure file share](?tabs=portal1%252cazure-portal#-2134351810) in de hand leiding voor probleem oplossing om dit probleem op te lossen. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Het bestand is versleuteld met een niet-ondersteunde oplossing (zoals NTFS EFS). | Het bestand ontsleutelen en een ondersteunde versleutelings oplossing gebruiken. Zie [Oplossingen voor versleuteling](./storage-sync-files-planning.md#encryption) in de planningshandleiding voor een lijst met ondersteunde oplossingen. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Het bestand bevindt zich in een gerepliceerde DFS-R-map met het kenmerk alleen-lezen. | Het bestand bevindt zich in een gerepliceerde DFS-R-map met het kenmerk alleen-lezen. Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie de [plannings handleiding](./storage-sync-files-planning.md#distributed-file-system-dfs) voor meer informatie. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand heeft de status verwijderen in behandeling. | U hoeft geen actie te ondernemen. Het bestand wordt verwijderd zodra alle geopende bestands ingangen zijn gesloten. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Het bestand kan niet worden gesynchroniseerd omdat de instellingen voor de firewall en het virtuele netwerk van het opslag account zijn ingeschakeld en de server geen toegang heeft tot het opslag account. | Voeg het IP-adres of het virtuele netwerk van de server toe aan de hand van de stappen die worden beschreven in de sectie [instellingen voor firewalls en virtuele netwerken configureren](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatie handleiding. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Het bestand kan niet worden gesynchroniseerd omdat de grootte van de security descriptor groter is dan de limiet van 64 KiB. | U kunt dit probleem oplossen door ACE’s (Access Control Entries) voor het bestand te verwijderen om de grootte van de security descriptor te verkleinen. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Het bestand kan niet worden gesynchroniseerd vanwege een onverwachte fout. | Als de fout gedurende enkele dagen blijft bestaan, kunt u een ondersteunings aanvraag openen. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand is tijdens de synchronisatie gewijzigd, dus het moet opnieuw worden gesynchroniseerd. | U hoeft geen actie te ondernemen. |
| 0x80070017 | -2147024873 | ERROR_CRC | Het bestand kan niet worden gesynchroniseerd vanwege een CRC-fout. Deze fout kan optreden als een gelaagd bestand niet is ingetrokken vóór het verwijderen van een server eindpunt of als het bestand is beschadigd. | Om dit probleem op te lossen, raadpleegt u [gelaagde bestanden niet toegankelijk op de server nadat u een server eindpunt hebt verwijderd](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) om gelaagde bestanden te verwijderen die zwevend zijn. Als de fout blijft optreden nadat u zwevende gelaagde bestanden hebt verwijderd, voert u [chkdsk](/windows-server/administration/windows-commands/chkdsk) uit op het volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Het bestand kan niet worden gesynchroniseerd omdat het maximum aantal conflict bestanden is bereikt. Azure File Sync ondersteunt 100-conflict bestanden per bestand. Zie Azure File Sync [Veelgestelde vragen](./storage-files-faq.md#afs-conflict-resolution)voor meer informatie over bestands conflicten. | Verminder het aantal conflict bestanden om dit probleem op te lossen. Het bestand wordt gesynchroniseerd zodra het aantal conflict bestanden kleiner is dan 100. |

#### <a name="handling-unsupported-characters"></a>Niet-ondersteunde tekens verwerken
Als in het Power shell-script van **FileSyncErrorsReport.ps1** per item synchronisatie fouten worden weer gegeven als gevolg van niet-ondersteunde tekens (fout code 0x8007007b of 0x80c80255), moet u de tekens bij fout verwijderen of de naam van de betreffende bestands namen wijzigen. In Power shell worden deze tekens waarschijnlijk afgedrukt als vraag tekens of lege rechthoeken omdat de meeste van deze tekens geen standaard-visuele code ring hebben. 
> [!Note]  
> Het [evaluatie hulpprogramma](storage-sync-files-planning.md#evaluation-cmdlet) kan worden gebruikt om tekens te identificeren die niet worden ondersteund. Als uw gegevensset meerdere bestanden met ongeldige tekens bevat, gebruikt u het script [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) om de namen van bestanden te wijzigen die niet-ondersteunde tekens bevatten.

De onderstaande tabel bevat alle Unicode-tekens Azure File Sync nog niet wordt ondersteund.

| Tekenset | Aantal tekens |
|---------------|-----------------|
| <ul><li>0x0000009D (opdracht osc-besturings systeem)</li><li>0x00000090 (DCS-besturings element voor Apparaatbeheer)</li><li>0x0000008F (ss3 één ploeg drie)</li><li>0x00000081 (definitie hoge octetten)</li><li>0x0000007F (del verwijderen)</li><li>0x0000008D (RI-omgekeerde regel invoer)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Arabische presentatie vormen-a) | 32 |
| 0x0000FFF0-0x0000FFFF (specials) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (niet-teken)</li><li>0x0002FFFE-0x0002FFFF = 2 (niet-teken)</li><li>0x0003FFFE-0x0003FFFF = 2 (niet-teken)</li><li>0x0004FFFE-0x0004FFFF = 2 (niet-teken)</li><li>0x0005FFFE-0x0005FFFF = 2 (niet-teken)</li><li>0x0006FFFE-0x0006FFFF = 2 (niet-teken)</li><li>0x0007FFFE-0x0007FFFF = 2 (niet-teken)</li><li>0x0008FFFE-0x0008FFFF = 2 (niet-teken)</li><li>0x0009FFFE-0x0009FFFF = 2 (niet-teken)</li><li>0x000AFFFE-0x000AFFFF = 2 (niet-teken)</li><li>0x000BFFFE-0x000BFFFF = 2 (niet-teken)</li><li>0x000CFFFE-0x000CFFFF = 2 (niet-teken)</li><li>0x000DFFFE-0x000DFFFF = 2 (niet-teken)</li><li>0x000EFFFE-0x000EFFFF = 2 (niet gedefinieerd)</li><li>0x000FFFFE-0x000FFFFF = 2 (aanvullend privé gebruik-gebied)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Algemene synchronisatie fouten
<a id="-2147023673"></a>**De synchronisatie sessie is geannuleerd.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimaal)** | -2147023673 | 
| **Fouttekenreeks** | ERROR_CANCELLED |
| **Herstel vereist** | No |

Synchronisatie sessies kunnen om verschillende redenen mislukken, zoals de server die opnieuw wordt opgestart of bijgewerkt, VSS-moment opnamen, enzovoort. Hoewel deze fout lijkt op opvolgen, is het veilig om deze fout te negeren, tenzij deze gedurende een periode van enkele uren persistent is.

<a id="-2147012889"></a>**Er kan geen verbinding worden gemaakt met de service.**    

| | |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (decimaal)** | -2147012889 | 
| **Fouttekenreeks** | WININET_E_NAME_NOT_RESOLVED |
| **Herstel vereist** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**De gebruikers aanvraag is beperkt door de service.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimaal)** | -2134376372 |
| **Fouttekenreeks** | ECS_E_USER_REQUEST_THROTTLED |
| **Herstel vereist** | No |

Er is geen actie vereist; de server zal het opnieuw proberen. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364043"></a>**Synchronisatie wordt geblokkeerd totdat de wijzigings detectie is voltooid na het herstellen**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimaal)** | -2134364043 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Herstel vereist** | No |

Geen actie vereist. Wanneer een bestand of bestands share (Cloud-eind punt) wordt hersteld met behulp van Azure Backup, wordt de synchronisatie geblokkeerd totdat de wijzigings detectie is voltooid op de Azure-bestands share. Wijzigingsdetectie wordt onmiddellijk uitgevoerd zodra het herstellen is voltooid. Hoe lang dit duurt is afhankelijk van het aantal bestanden in de bestandsshare.

<a id="-2147216747"></a>**De synchronisatie is mislukt, omdat de synchronisatie database is verwijderd.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimaal)** | -2147216747 |
| **Fouttekenreeks** | SYNC_E_METADATA_INVALID_OPERATION |
| **Herstel vereist** | No |

Deze fout treedt doorgaans op wanneer met een back-uptoepassing een VSS-momentopname wordt gemaakt en de Sync-database is verwijderd. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364065"></a>**Sync heeft geen toegang tot de Azure-bestands share die is opgegeven in het Cloud eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimaal)** | -2134364065 |
| **Fouttekenreeks** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang kan krijgen tot de Azure-bestandsshare. Dit kan komen doordat de Azure-bestandsshare of het opslagaccount waar deze wordt gehost, niet meer bestaat. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)
3. [Zorg ervoor dat Azure File Sync toegang heeft tot het opslag account.](#troubleshoot-rbac)
4. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**De synchronisatie is mislukt, omdat de aanvraag niet is gemachtigd om deze bewerking uit te voeren.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimaal)** | -2134351804 |
| **Fouttekenreeks** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang heeft tot de Azure-bestands share. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Zorg ervoor dat Azure File Sync toegang heeft tot het opslag account.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**De gebruikte naam van het opslag account kan niet worden omgezet.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimaal)** | -2134364064 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Herstel vereist** | Yes |

1. Controleer of u de DNS-naam van de opslag van de server kunt omzetten.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Er is een onbekende fout opgetreden bij het openen van het opslag account.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimaal)** | -2134364022 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Herstel vereist** | Yes |

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**De synchronisatie is mislukt omdat het opslag account is vergrendeld.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimaal)** | -2134364014 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het opslag account een alleen-lezen [bron vergrendeling](../../azure-resource-manager/management/lock-resources.md)heeft. U kunt dit probleem oplossen door de alleen-lezen resource vergrendeling voor het opslag account te verwijderen. 

<a id="-1906441138"></a>**De synchronisatie is mislukt vanwege een probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimaal)** | -1906441138 |
| **Fouttekenreeks** | JET_errWriteConflict |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer er een probleem is met de interne data base die wordt gebruikt door Azure File Sync. Als dit probleem zich voordoet, maakt u een ondersteunings aanvraag en neemt u contact met u op om dit probleem op te lossen.

<a id="-2134364053"></a>**De versie van de Azure File Sync-agent die op de server is geïnstalleerd, wordt niet ondersteund.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimaal)** | -2134364053 |
| **Fouttekenreeks** | ECS_E_AGENT_VERSION_BLOCKED |
| **Herstel vereist** | Yes |

Deze fout treedt op als de versie van de Azure File Sync-agent op de server wordt niet ondersteund. U kunt dit probleem oplossen door een [upgrade uit]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) te voeren naar een [ondersteunde agent versie]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**U hebt de opslag limiet voor Azure file share bereikt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimaal)** | -2134351810 |
| **Fouttekenreeks** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de opslaglimiet van de Azure-bestandsshare is bereikt. Dit kan gebeuren als er een quotum voor een Azure-bestandsshare is ingesteld of als een gebruikslimiet voor een Azure-bestandsshare is overschreden. Zie de [huidige limieten voor een Azure-bestands share](storage-files-scale-targets.md)voor meer informatie.

1. Ga naar de synchronisatie groep binnen de opslag synchronisatie service.
2. Selecteer het eind punt van de cloud in de synchronisatie groep.
3. Noteer de naam van de Azure-bestands share in het geopende deel venster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslag account waarnaar wordt verwezen, verwijderd.

    ![Een scherm afbeelding met het detail venster van het Cloud eindpunt met een koppeling naar het opslag account.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecteer **bestanden** om de lijst met bestands shares weer te geven.
6. Klik op de drie punten aan het einde van de rij voor de Azure-bestands share waarnaar wordt verwezen door het Cloud eindpunt.
7. Controleer of het **gebruik** lager is dan het **quotum**. Opmerking tenzij er een alternatieve quotum is opgegeven, komt het quotum overeen met de [maximale grootte van de Azure-bestands share](storage-files-scale-targets.md).

    ![Een scherm afbeelding van de eigenschappen van de Azure-bestands share.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Als de share vol is en er geen quotum is ingesteld, kunt u dit probleem oplossen door elke submap van het huidige servereindpunt te maken in een eigen servereindpunt en een eigen synchronisatiegroep. Zo wordt de verschillende submappen gesynchroniseerd met verschillende Azure-bestandsshares.

<a id="-2134351824"></a>**De Azure-bestands share is niet gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimaal)** | -2134351824 |
| **Fouttekenreeks** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is. Ga als volgt te werk om het probleem op te lossen:

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)

Als de Azure-bestands share is verwijderd, moet u een nieuwe bestands share maken en de synchronisatie groep vervolgens opnieuw maken. 

<a id="-2134364042"></a>**De synchronisatie is onderbroken terwijl dit Azure-abonnement is onderbroken.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimaal)** | -2134364042 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer het Azure-abonnement is onderbroken. Synchronisatie wordt opnieuw ingeschakeld wanneer het Azure-abonnement wordt hersteld. Zie [Waarom is mijn Azure-abonnement uitgeschakeld en hoe kan ik het opnieuw activeren?](../../cost-management-billing/manage/subscription-disabled.md) voor meer informatie.

<a id="-2134375618"></a>**Er is een firewall of virtuele netwerken geconfigureerd voor het opslag account.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (decimaal)** | -2134375618 |
| **Fouttekenreeks** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Herstel vereist** | Yes |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is vanwege een firewall bij een opslagaccount, of omdat het opslagaccount deel uitmaakt van een virtueel netwerk. Controleer of de instellingen voor de firewall en het virtuele netwerk van het opslag account correct zijn geconfigureerd. Zie [instellingen voor Firewall en virtueel netwerk configureren](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)voor meer informatie. 

<a id="-2134375911"></a>**De synchronisatie is mislukt vanwege een probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimaal)** | -2134375911 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Herstel vereist** | No |

Deze fout wordt meestal vanzelf opgelost, en kan optreden wanneer:

* Een groot aantal bestands wijzigingen op de servers in de synchronisatie groep.
* Een groot aantal fouten op afzonderlijke bestanden en mappen.

Als deze fout langer dan een paar uur blijft bestaan, maakt u een ondersteunings aanvraag en neemt u contact met u op om dit probleem op te lossen.

<a id="-2146762487"></a>**De server kan geen beveiligde verbinding tot stand brengen. De Cloud service heeft een onverwacht certificaat ontvangen.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimaal)** | -2146762487 |
| **Fouttekenreeks** | CERT_E_UNTRUSTEDROOT |
| **Herstel vereist** | Yes |

Deze fout kan optreden als uw organisatie gebruikmaakt van een TLS-beëindigings proxy of als een schadelijke entiteit het verkeer tussen uw server en de Azure File Sync-service onderschept. Als u zeker weet dat dit wordt verwacht (omdat uw organisatie gebruikmaakt van een TLS-beëindigings proxy), slaat u de certificaat verificatie over met een overschrijving van het REGI ster.

1. Maak de register waarde SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Start de synchronisatie service op de geregistreerde server opnieuw op.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Door deze register waarde in te stellen, accepteert de Azure File Sync-agent elk lokaal vertrouwd TLS/SSL-certificaat wanneer gegevens tussen de server en de Cloud service worden overgedragen.

<a id="-2147012894"></a>**Er kan geen verbinding worden gemaakt met de service.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimaal)** | -2147012894 |
| **Fouttekenreeks** | WININET_E_TIMEOUT |
| **Herstel vereist** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**De synchronisatie is mislukt vanwege een probleem met de verificatie.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimaal)** | -2134375680 |
| **Fouttekenreeks** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Herstel vereist** | Yes |

Deze fout wordt meestal veroorzaakt door een onjuiste servertijd. Als de server wordt uitgevoerd op een virtuele machine, controleert u of de tijd op de host juist is.

<a id="-2134364040"></a>**De synchronisatie is mislukt vanwege een verlopen certificaat.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimaal)** | -2134364040 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, is verlopen.

Voer de volgende stappen uit om te controleren of het certificaat is verlopen:  
1. Open de MMC-module Certificaten, selecteer computer account en navigeer naar certificaten (lokale computer) \Personal\Certificates.
2. Controleer of het certificaat voor client verificatie is verlopen.

Als het certificaat voor clientverificatie is verlopen, voert u de volgende stappen uit om het probleem op te lossen:

1. Controleer of de Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**De synchronisatie is mislukt vanwege een verificatie certificaat dat niet is gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimaal)** | -2134375896 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, niet is gevonden.

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Controleer of de Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**De synchronisatie is mislukt omdat de verificatie-identiteit niet is gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimaal)** | -2134364039 |
| **Fouttekenreeks** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het verwijderen van het servereindpunt is mislukt en het eindpunt nu een gedeeltelijk verwijderde status heeft. Probeer het servereindpunt opnieuw te verwijderen om dit probleem op te lossen.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Het volume waar het server eindpunt zich bevindt, heeft weinig schijf ruimte.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimaal)** | -1906441711 |
| **Fouttekenreeks** | JET_errLogDiskFull |
| **Herstel vereist** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimaal)** | -2134375654 |
| **Fouttekenreeks** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het volume vol raakt. Deze fout treedt doorgaans op omdat bestanden buiten het servereindpunt ruimte gebruiken op het volume. Maak ruimte vrij op het volume door extra server eindpunten toe te voegen, bestanden te verplaatsen naar een ander volume of de grootte van het volume te verg Roten waarop het server eindpunt zich bevindt.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**De service is nog niet gereed om te synchroniseren met dit server eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimaal)** | -2134364145 |
| **Fouttekenreeks** | ECS_E_REPLICA_NOT_READY |
| **Herstel vereist** | No |

Deze fout treedt op omdat het Cloud eindpunt is gemaakt met inhoud die al aanwezig is op de Azure-bestands share. Azure File Sync moet de Azure-bestands share voor alle inhoud scannen voordat de initiële synchronisatie van het server eindpunt kan worden voortgezet.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**De synchronisatie is mislukt vanwege problemen met een groot aantal afzonderlijke bestanden.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimaal)** | -2134375877 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Herstel vereist** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimaal)** | -2134375908 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Herstel vereist** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimaal)** | -2134375853 |
| **Fouttekenreeks** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Herstel vereist** | Yes |

Synchronisatie sessies mislukken met een van deze fouten wanneer er veel bestanden zijn die niet kunnen worden gesynchroniseerd met fouten per item. Voer de stappen uit die worden beschreven in de [Hoe kan ik controleren of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) sectie voor het oplossen van fouten per item. Open een ondersteunings aanvraag voor een synchronisatie fout ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED.

> [!NOTE]
> Azure File Sync maakt een tijdelijke VSS-moment opname eenmaal per dag op de server om bestanden met geopende ingangen te synchroniseren.

<a id="-2134376423"></a>**De synchronisatie is mislukt vanwege een probleem met het pad naar het server eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimaal)** | -2134376423 |
| **Fouttekenreeks** | ECS_E_SYNC_INVALID_PATH |
| **Herstel vereist** | Yes |

Zorg ervoor dat het pad bestaat, zich op een lokaal NTFS-volume bevindt en geen reparsepunt of bestaand server eindpunt is.

<a id="-2134375817"></a>**De synchronisatie is mislukt, omdat de versie van het filter stuur programma niet compatibel is met de agent versie**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimaal)** | -2134375817 |
| **Fouttekenreeks** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Herstel vereist** | Yes |

Deze fout komt doordat de geladen versie van het filterstuurprogramma voor opslag in cloudlagen (StorageSync.sys) niet compatibel is met de Storage Sync Agent-service (FileSyncSvc). Als de Azure File Sync-agent is bijgewerkt, start u de server opnieuw op om de installatie te voltooien. Als de fout blijft optreden, verwijdert u de agent, start u de server opnieuw op en installeert u de Azure File Sync-agent opnieuw.

<a id="-2134376373"></a>**De service is momenteel niet beschikbaar.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimaal)** | -2134376373 |
| **Fouttekenreeks** | ECS_E_SERVICE_UNAVAILABLE |
| **Herstel vereist** | No |

Deze fout treedt op omdat de Azure File Sync-service niet beschikbaar is. Deze fout wordt automatisch opgelost wanneer de Azure File Sync-service weer beschikbaar is.

<a id="-2146233088"></a>**De synchronisatie is mislukt vanwege een uitzonde ring.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimaal)** | -2146233088 |
| **Fouttekenreeks** | COR_E_EXCEPTION |
| **Herstel vereist** | No |

Deze fout treedt op omdat zich in Sync een uitzondering voordoet. Als de fout gedurende enkele uren blijft bestaan, moet u een ondersteunings aanvraag maken.

<a id="-2134364045"></a>**De synchronisatie is mislukt, omdat er een failover voor het opslag account is uitgevoerd naar een andere regio.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimaal)** | -2134364045 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat het opslagaccount is overgeschakeld naar een andere regio. Failover-overschakeling van het opslagaccount wordt niet ondersteund in Azure File Sync. Er mag geen failover-overschakeling worden uitgevoerd voor opslagaccounts met Azure-bestandsshares die worden gebruikt als cloudeindpunten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen. U kunt dit probleem oplossen door het opslagaccount te verplaatsen naar de primaire regio.

<a id="-2134375922"></a>**De synchronisatie is mislukt vanwege een tijdelijk probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimaal)** | -2134375922 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Herstel vereist** | No |

Deze fout treedt op vanwege een intern probleem met de synchronisatiedatabase. Deze fout wordt automatisch opgelost bij nieuwe synchronisatiepogingen. Als deze fout gedurende een verlengde periode blijft bestaan, kunt u een ondersteunings aanvraag maken. we nemen contact met u op om dit probleem op te lossen.

<a id="-2134364024"></a>**De synchronisatie is mislukt vanwege een wijziging in de Azure Active Directory Tenant**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimaal)** | -2134364024 | 
| **Fouttekenreeks** | ECS_E_INVALID_AAD_TENANT |
| **Herstel vereist** | Yes |

Zorg ervoor dat u de nieuwste Azure File Sync-agent hebt. Vanaf agent V10 toevoegen biedt Azure File Sync ondersteuning voor het verplaatsen van het abonnement naar een andere Azure Active Directory-Tenant.
 
Zodra u de nieuwste versie van de agent hebt, moet u de toepassing micro soft. StorageSync toegang geven tot het opslag account (Zie [controleren of Azure file sync toegang heeft tot het opslag account](#troubleshoot-rbac)).

<a id="-2134364010"></a>**De synchronisatie is mislukt omdat de firewall en de uitzonde ring voor het virtuele netwerk niet zijn geconfigureerd**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimaal)** | -2134364010 | 
| **Fouttekenreeks** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Herstel vereist** | Yes |

Deze fout treedt op als de instellingen voor de firewall en het virtuele netwerk zijn ingeschakeld voor het opslag account en de uitzonde ring ' vertrouwde micro soft-Services voor toegang tot dit opslag account toestaan ' niet is ingeschakeld. U kunt dit probleem oplossen door de stappen te volgen die worden beschreven in de sectie [Instellingen voor de firewall en het virtuele netwerk configureren](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatiehandleiding.

<a id="-2147024891"></a>**De synchronisatie is mislukt, omdat de machtigingen voor de map System Volume Information onjuist zijn.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimaal)** | -2147024891 |
| **Fouttekenreeks** | ERROR_ACCESS_DENIED |
| **Herstel vereist** | Yes |

Deze fout kan optreden als het NT AUTHORITY\SYSTEM-account geen machtigingen heeft voor de map System Volume Information op het volume waar het servereindpunt zich bevindt. Als afzonderlijke bestanden niet kunnen worden gesynchroniseerd met ERROR_ACCESS_DENIED, voert u de stappen uit die worden beschreven in de sectie [problemen oplossen per bestand/directory-synchronisatie fouten](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Download het hulpprogramma [PsExec](/sysinternals/downloads/psexec).
2. Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om een opdracht prompt te starten met het systeem account: **PsExec.exe-i-s-d cmd** 
3. Voer de volgende opdracht uit vanaf de opdrachtprompt die wordt uitgevoerd met het systeemaccount om te controleren of het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information: **cacls "stationsletter:\system volume information" /T /C**
4. Als het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information, voert u de volgende opdracht uit: **cacls "stationsletter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM: F"**
    - Als stap 4 mislukt omdat de toegang wordt geweigerd, voert u de volgende opdracht uit om eigenaar van de map System Volume Information te worden en herhaalt u vervolgens stap 4: **takeown /A /R /F "stationsletter:\System Volume Information"**

<a id="-2134375810"></a>**De synchronisatie is mislukt, omdat de Azure-bestands share is verwijderd en opnieuw is gemaakt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimaal)** | -2134375810 |
| **Fouttekenreeks** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat Azure File Sync geen ondersteuning biedt voor het verwijderen en opnieuw maken van een Azure-bestandsshare in dezelfde synchronisatiegroep. 

U kunt dit probleem oplossen door de synchronisatiegroep te verwijderen en opnieuw te maken door de volgende stappen uit te voeren:

1. Verwijder alle server eindpunten in de synchronisatie groep.
2. Verwijder het Cloud eindpunt. 
3. Verwijder de synchronisatiegroep.
4. Als Cloud lagen zijn ingeschakeld op een server eindpunt, verwijdert u de zwevende gelaagde bestanden op de server door de stappen uit te voeren die worden beschreven in de [gelaagde bestanden niet toegankelijk zijn op de server na het verwijderen van een server eindpunt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) sectie.
5. Maak de synchronisatie groep opnieuw.

<a id="-2145844941"></a>**De synchronisatie is mislukt, omdat de HTTP-aanvraag is omgeleid**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimaal)** | -2145844941 |
| **Fouttekenreeks** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Herstel vereist** | Yes |

Deze fout treedt op omdat Azure File Sync HTTP-omleiding (status code 3xx) niet ondersteunt. Om dit probleem op te lossen, schakelt u HTTP-omleiding uit op de proxy server of het netwerk apparaat.

<a id="-2134364027"></a>**Er is een time-out opgetreden tijdens de offline gegevens overdracht, maar deze wordt nog uitgevoerd.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimaal)** | -2134364027 |
| **Fouttekenreeks** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Herstel vereist** | No |

Deze fout treedt op wanneer een bewerking voor gegevens opname de time-out overschrijdt. Deze fout kan worden genegeerd als voortgang van de synchronisatie plaatsvindt (AppliedItemCount is groter dan 0). Zie [Hoe kan ik de voortgang van een huidige synchronisatie sessie controleren?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**De synchronisatie is mislukt omdat het pad naar het server eindpunt niet kan worden gevonden op de server.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (decimaal)** | -2134375814 |
| **Fouttekenreeks** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Herstel vereist** | Yes |

Deze fout treedt op als de naam van de map die wordt gebruikt voor het pad van het server eindpunt, is gewijzigd of als deze is verwijderd. Als de naam van de map is gewijzigd, wijzigt u de naam van de map terug naar de oorspronkelijke server en start u de Storage Sync Agent-service (FileSyncSvc) opnieuw.

Als de map is verwijderd, voert u de volgende stappen uit om het bestaande server eindpunt te verwijderen en een nieuw server eindpunt te maken met een nieuw pad:

1. Verwijder het server eindpunt in de synchronisatie groep door de stappen te volgen die worden beschreven in [een server eindpunt verwijderen](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).
2. Maak een nieuw server eindpunt in de synchronisatie groep door de stappen te volgen die worden beschreven in [een server eindpunt toevoegen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#add-a-server-endpoint).

### <a name="common-troubleshooting-steps"></a>Veelvoorkomende stappen voor probleem oplossing
<a id="troubleshoot-storage-account"></a>**Controleer of het opslag account bestaat.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Ga naar de synchronisatie groep binnen de opslag synchronisatie service.
2. Selecteer het eind punt van de cloud in de synchronisatie groep.
3. Noteer de naam van de Azure-bestands share in het geopende deel venster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslag account waarnaar wordt verwezen, verwijderd.
    ![Een scherm afbeelding met het detail venster van het Cloud eindpunt met een koppeling naar het opslag account.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

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
1. Klik op **overzicht** in de linkerkolom met inhoud om terug te keren naar de pagina voor het hoofd-opslag account.
2. Selecteer **bestanden** om de lijst met bestands shares weer te geven.
3. Controleer of de bestands share waarnaar wordt verwezen door het Cloud-eind punt wordt weer gegeven in de lijst met bestands shares (u hebt dit in stap 1 hierboven vermeld).

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

<a id="troubleshoot-rbac"></a>**Zorg ervoor dat Azure File Sync toegang heeft tot het opslag account.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klik op **toegangs beheer (IAM)** in de linker inhouds opgave.
1. Klik op **het tabblad roltoewijzingen** aan de lijst met gebruikers en toepassingen (*service-principals*) die toegang hebben tot uw opslag account.
1. Controleer of de service **micro soft. StorageSync** of **Hybrid file sync** (oude toepassings naam) wordt weer gegeven in de lijst met de rol **lezer en gegevens toegang** . 

    ![Een scherm afbeelding van de Service-Principal Hybrid File Sync Service op het tabblad toegangs beheer van het opslag account](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Als de service **micro soft. StorageSync** of **Hybrid file sync** niet in de lijst wordt weer gegeven, voert u de volgende stappen uit:

    - Klik op **Add**.
    - Selecteer in het veld **rol** de optie **lezer en gegevens toegang**.
    - Typ **micro soft. StorageSync** in het veld **selecteren** , selecteer de rol en klik op **Opslaan**.

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

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hoe kan ik voor komen dat gebruikers bestanden met niet-ondersteunde tekens op de server maken?
U kunt bestands [Server bron beheer (FSRM)-bestands controles](/windows-server/storage/fsrm/file-screening-management) gebruiken om bestanden te blok keren waarbij niet-ondersteunde tekens in hun namen worden gemaakt op de server. Mogelijk moet u dit doen met behulp van Power shell als het meren deel van de niet-ondersteunde tekens niet kan worden afgedrukt en dus moet u de hexadecimale weer gaven als tekens eerst converteren.

Maak eerst een bestands groep FSRM met de [cmdlet New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). In dit voor beeld wordt de groep zodanig gedefinieerd dat deze slechts twee van de niet-ondersteunde tekens bevat, maar u kunt zo veel van de tekens opnemen die nodig zijn in uw bestands groep.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Zodra u een FSRM-bestands groep hebt gedefinieerd, kunt u een bestandssysteem FSRM maken met behulp van de cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Houd er rekening mee dat bestands controles alleen kunnen worden gebruikt om het maken van tekens te blok keren die niet door Azure File Sync worden ondersteund. Als bestands controles in andere scenario's worden gebruikt, probeert synchronisatie voortdurend de bestanden te downloaden van de Azure-bestands share naar de server en wordt deze geblokkeerd als gevolg van de bestands controle, wat resulteert in hoge gegevens uitvoer. 

## <a name="cloud-tiering"></a>Cloudopslaglagen 
Er zijn twee paden voor fouten in Cloud lagen:

- Bestanden kunnen niet op de juiste manier worden gelaagd. Dit betekent dat Azure File Sync geen geslaagde poging om een bestand te tierren naar Azure Files.
- Bestanden kunnen niet worden ingetrokken. Dit betekent dat de Azure File Sync bestandssysteem filter (StorageSync.sys) geen gegevens kan downloaden wanneer een gebruiker toegang probeert te krijgen tot een bestand dat is gelaagd.

Er zijn twee hoofd klassen van fouten die kunnen optreden via een fout-pad:

- Fouten in de Cloud opslag
    - *Problemen met de beschik baarheid van tijdelijke opslag Services*. Zie voor meer informatie de [Service Level Agreement (Sla) voor Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - De *Azure-bestands share* is niet toegankelijk. Deze fout treedt doorgaans op wanneer u de Azure-bestands share verwijdert wanneer deze nog steeds een Cloud-eind punt in een synchronisatie groep is.
    - Het *opslag account* is niet toegankelijk. Deze fout treedt doorgaans op wanneer u het opslag account verwijdert terwijl het nog een Azure-bestands share is die een Cloud-eind punt in een synchronisatie groep is. 
- Server fouten 
  - *Azure File Sync File System filter (StorageSync.sys) is niet geladen*. Als u wilt reageren op aanvragen voor lagen/terughalen, moet het Azure File Sync bestandssysteem filter worden geladen. Het filter dat niet wordt geladen, kan om verschillende redenen plaatsvinden, maar de meest voorkomende reden is dat een beheerder deze hand matig heeft verwijderd. Het Azure File Sync-bestandssysteem filter moet altijd worden geladen om Azure File Sync goed te kunnen functioneren.
  - *Ontbrekend, beschadigd of anderszins gebroken reparsepunt*. Een reparsepunt is een speciale gegevens structuur voor een bestand dat bestaat uit twee delen:
    1. Een reparsecode, die het besturings systeem aangeeft dat het Azure File Sync File System filter (StorageSync.sys) een bepaalde actie moet uitvoeren op IO naar het bestand. 
    2. Reparsegegevens, waarmee wordt aangegeven dat het bestandssysteem de URI van het bestand op het gekoppelde cloud eindpunt (de Azure-bestands share) wordt gefilterd. 
        
       De meest voorkomende manier waarop een reparsepunt beschadigd kan raken, is als een beheerder de tag of de gegevens ervan probeert te wijzigen. 
  - *Problemen met de netwerk verbinding*. Als u een bestand wilt ordenen of intrekken, moet de server verbinding hebben met internet.

De volgende secties geven informatie over het oplossen van problemen met Cloud lagen en bepalen of een probleem een probleem met de Cloud opslag of een server probleem is.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Activiteit van opslaglagen op een server bewaken  
Gebruik gebeurtenis-ID 9003, 9016 en 9029 in het telemetrie-gebeurtenis logboek (bevindt zich onder toepassingen en Services\Microsoft\FileSync\Agent in Logboeken) om de activiteit voor het bijhouden van lagen op een server te bewaken.

- Gebeurtenis-ID 9003 biedt fout distributie voor een server eindpunt. Bijvoorbeeld totaal aantal fouten, error code, enzovoort. Opmerking: er wordt één gebeurtenis geregistreerd per fout code.
- Gebeurtenis-ID 9016 bevat spook resultaten voor een volume. Bijvoorbeeld: percentage vrije ruimte is het aantal bestanden dat is gedupliceerd in de sessie, het aantal bestanden dat niet kan worden Ghost, enzovoort.
- Gebeurtenis-ID 9029 bevat informatie over ghost-sessies voor een server eindpunt. Bijvoorbeeld het aantal bestanden dat is geprobeerd in de sessie, het aantal bestanden dat is gelaagd in de sessie, het aantal bestanden dat al is gelaagd, enzovoort.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Terughaalactiviteit op een server bewaken
Gebruik gebeurtenis-ID 9005, 9006, 9009 en 9059 in het telemetrie-gebeurtenis logboek (onder toepassingen en Services\Microsoft\FileSync\Agent in Logboeken) voor het bewaken van inhaal activiteiten op een server.

- Gebeurtenis-ID 9005 biedt betrouw baarheid van een server eindpunt. Bijvoorbeeld: totale aantal geopende bestanden, unieke bestanden met mislukte toegang, enzovoort.
- Gebeurtenis-ID 9006 biedt fout bij het intrekken van fouten voor een server eindpunt. Bijvoorbeeld totaal aantal mislukte aanvragen, error code, enzovoort. Opmerking: er wordt één gebeurtenis geregistreerd per fout code.
- Gebeurtenis-ID 9009 biedt informatie over het intrekken van sessies voor een server eindpunt. Bijvoorbeeld, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- Met gebeurtenis-ID 9059 kan de toepassing worden gedistribueerd voor een server eindpunt. Bijvoorbeeld ShareId, toepassings naam en TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Problemen oplossen met bestanden die niet kunnen worden getierd
Als bestanden niet naar Azure Files kunnen worden gelaagd:

1. Bekijk in Logboeken de telemetrie-, operationele en diagnostische gebeurtenis logboeken, die zich bevinden onder toepassingen en Services\Microsoft\FileSync\Agent. 
   1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.

      > [!NOTE]
      > Een bestand moet worden gesynchroniseerd met een Azure-bestands share voordat het kan worden getierd.

   2. Controleer of de server verbinding met internet heeft. 
   3. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
       - Voer uit vanaf een opdracht prompt met verhoogde bevoegdheid `fltmc` . Controleer of de StorageSync.sys en StorageSyncGuard.sys Stuur Programma's voor het bestands systeem worden weer gegeven.

> [!NOTE]
> Gebeurtenis-ID 9003 wordt eenmaal per uur vastgelegd in het logboek voor telemetrie als een bestand niet kan worden gelaagd (één gebeurtenis wordt per fout code geregistreerd). Controleer de sectie [fouten in lagen en herstel](#tiering-errors-and-remediation) om te zien of er herstels tappen voor de fout code worden weer gegeven.

### <a name="tiering-errors-and-remediation"></a>Fouten in lagen en herstel

| HRESULT | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Het bestand kan niet worden getierd omdat de eerste upload bewerking wordt uitgevoerd. | U hoeft geen actie te ondernemen. Het bestand wordt getierd zodra de eerste upload is voltooid. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Het bestand kan niet worden getierd omdat het in gebruik is. | U hoeft geen actie te ondernemen. Het bestand wordt getierd wanneer het niet langer in gebruik is. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Het bestand kan niet worden getierd omdat het is uitgesloten door de synchronisatie. | U hoeft geen actie te ondernemen. De bestanden in de lijst met uitgesloten synchronisatie kunnen niet worden getierd. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Het bestand kan niet worden getierd omdat het niet is gevonden op de server. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u of het bestand zich op de server bevindt. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Het bestand kan niet worden getierd omdat het is verwijderd in de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand moet op de server worden verwijderd wanneer de volgende down load-synchronisatie sessie wordt uitgevoerd. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Het bestand kan niet worden gelaagd vanwege een probleem met het netwerk. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerk verbinding met de Azure-bestands share. |
| 0x80072EE7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Het bestand kan niet worden gelaagd vanwege een probleem met het netwerk. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerk verbinding met de Azure-bestands share. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand kan niet worden gelaagd vanwege een fout bij het weigeren van de toegang. Deze fout kan optreden als het bestand zich bevindt in een gerepliceerde map met het kenmerk alleen-lezen van DFS-R. | Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie de [plannings handleiding](./storage-sync-files-planning.md#distributed-file-system-dfs) voor meer informatie. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Het bestand kan niet worden gelaagd vanwege een probleem met het netwerk. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerk verbinding met de Azure-bestands share. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Het bestand kan niet worden getierd omdat de bestands grootte kleiner is dan de ondersteunde grootte. | Als de agent versie lager is dan 9,0, is de mini maal ondersteunde bestands grootte 64 kB. Als de agent versie 9,0 en hoger is, is de mini maal ondersteunde bestands grootte gebaseerd op de grootte van het bestandssysteem cluster (Double File System cluster size). Als de cluster grootte van het bestands systeem bijvoorbeeld 4kb is, is de minimale bestands grootte 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Het bestand kan niet worden gelaagd vanwege een probleem met Azure Storage. | Als de fout zich blijft voordoen, opent u een ondersteunings aanvraag. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Het bestand kan niet worden getierd omdat het tegelijkertijd is ingetrokken. | U hoeft geen actie te ondernemen. Het bestand wordt getierd wanneer het intrekken is voltooid en het bestand wordt niet meer gebruikt. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Het bestand kan niet worden getierd omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestands share. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Het bestand kan niet worden getierd omdat het filter stuur programma voor Cloud lagen (storagesync.sys) niet wordt uitgevoerd. | Om dit probleem op te lossen, opent u een opdracht prompt met verhoogde bevoegdheid en voert u de volgende opdracht uit: `fltmc load storagesync`<br>Als het storagesync-filter stuur programma niet kan worden geladen tijdens het uitvoeren van de Fltmc-opdracht, verwijdert u de agent van Azure File Sync, start u de server opnieuw op en installeert u de Azure File Sync agent opnieuw. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand kan niet worden gelaagd vanwege onvoldoende schijf ruimte op het volume waar het server eindpunt zich bevindt. | U kunt dit probleem oplossen door ten minste 100 MB aan schijf ruimte vrij te maken op het volume waar het server eindpunt zich bevindt. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Het bestand kan niet worden getierd omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestands share. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Het bestand kan niet worden getierd omdat het een niet-ondersteund reparsepunt is. | Als het bestand een reparsepunt voor Gegevensontdubbeling is, volgt u de stappen in de [plannings handleiding](./storage-sync-files-planning.md#data-deduplication) om ondersteuning van gegevensontdubbeling in te scha kelen. Bestanden met een andere reparse-punten dan Gegevensontdubbeling worden niet ondersteund en worden niet getierd.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Het bestand kan niet worden getierd omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestands share. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Het bestand kan niet worden getierd omdat het niet is gesynchroniseerd met de Azure-bestands share. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestands share. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Het bestand kan niet worden gelaagd vanwege een probleem met het netwerk. | U hoeft geen actie te ondernemen. Als de fout zich blijft voordoen, controleert u de netwerk verbinding met de Azure-bestands share. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand kan niet worden getierd omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestands share. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand kan niet worden gelaagd vanwege onvoldoende systeem resources. | Als de fout zich blijft voordoen, onderzoekt u welke toepassing of kernelmodusstuurprogramma de systeem bronnen uitgeput raken. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Het bestand kan niet worden gelaagd vanwege een I/O-fout bij het schrijven naar de Cloud-Tiering-data base. | Als de fout zich blijft voordoen, voert u Chkdsk op het volume uit en controleert u de opslaghardware. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Het bestand kan niet worden getierd omdat de Data Base voor Cloud lagen niet actief is. | U kunt dit probleem oplossen door de FileSyncSvc-service of-server opnieuw te starten. Als de fout zich blijft voordoen, voert u Chkdsk op het volume uit en controleert u de opslaghardware. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Problemen oplossen met bestanden die niet worden ingetrokken  
Als bestanden niet kunnen worden ingetrokken:
1. Bekijk in Logboeken de telemetrie-, operationele en diagnostische gebeurtenis logboeken, die zich bevinden onder toepassingen en Services\Microsoft\FileSync\Agent.
    1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.
    2. Controleer of de server verbinding met internet heeft. 
    3. Open de MMC-module Services en controleer of de opslag synchronisatie Agent-service (FileSyncSvc) wordt uitgevoerd.
    4. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
        - Voer uit vanaf een opdracht prompt met verhoogde bevoegdheid `fltmc` . Controleer of de StorageSync.sys en StorageSyncGuard.sys Stuur Programma's voor het bestands systeem worden weer gegeven.

> [!NOTE]
> Gebeurtenis-ID 9006 wordt eenmaal per uur vastgelegd in het logboek voor telemetrie als een bestand niet kan worden ingetrokken (één gebeurtenis wordt per fout code geregistreerd). Raadpleeg de sectie [fouten intrekken en herstellen](#recall-errors-and-remediation) om te zien of er herstels tappen voor de fout code worden weer gegeven.

### <a name="recall-errors-and-remediation"></a>Fouten en herstel intrekken

| HRESULT | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Kan het bestand niet intrekken vanwege een I/O-time-out. Dit probleem kan verschillende oorzaken hebben: Server Resource beperkingen, slechte netwerk verbinding of een probleem met Azure-opslag (bijvoorbeeld beperking). | U hoeft geen actie te ondernemen. Als de fout gedurende enkele uren blijft bestaan, opent u een ondersteuningscase. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Het bestand is niet ingetrokken vanwege een netwerk probleem.  | Als de fout zich blijft voordoen, controleert u de netwerk verbinding met de Azure-bestands share. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Het bestand kan niet worden ingetrokken omdat het server eindpunt is verwijderd. | Zie [gelaagde bestanden niet toegankelijk op de server nadat u een server eindpunt hebt verwijderd](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand kan niet worden ingetrokken vanwege een fout bij het weigeren van de toegang. Dit probleem kan optreden als de instellingen voor de firewall en het virtuele netwerk van het opslag account zijn ingeschakeld en de server geen toegang heeft tot het opslag account. | U kunt dit probleem oplossen door het IP-adres of het virtuele netwerk van de server toe te voegen aan de hand van de stappen die worden beschreven in de sectie [instellingen voor firewalls en virtuele netwerken configureren](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatie handleiding. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Het bestand kan niet worden ingetrokken omdat het niet toegankelijk is in de Azure-bestands share. | Controleer of het bestand bestaat in de Azure-bestands share om dit probleem op te lossen. Als het bestand zich in de Azure-bestands share bevindt, moet u een upgrade uitvoeren naar de nieuwste versie van Azure File Sync [agent](./storage-files-release-notes.md#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Het bestand kan niet worden ingetrokken vanwege een autorisatie fout naar het opslag account. | Als u dit probleem wilt oplossen, controleert [u Azure file sync toegang heeft tot het opslag account](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Het bestand kan niet worden ingetrokken omdat de Azure-bestands share niet toegankelijk is. | Controleer of de bestands share bestaat en toegankelijk is. Als de bestands share is verwijderd en opnieuw is gemaakt, voert u de stappen uit die worden beschreven in de [synchronisatie is mislukt, omdat de Azure-bestands share is verwijderd en opnieuw is gemaakt](?tabs=portal1%252cazure-portal#-2134375810) om de synchronisatie groep te verwijderen en opnieuw te maken. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand is niet ingetrokken vanwege onvoldoende systeem resources. | Als de fout zich blijft voordoen, onderzoekt u welke toepassing of kernelmodusstuurprogramma de systeem bronnen uitgeput raken. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Kan het bestand niet intrekken vanwege onvoldoende geheugen. | Als de fout zich blijft voordoen, onderzoekt u welke toepassing of kernelmodus het stuur programma voor onvoldoende geheugen veroorzaakt. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand is niet ingetrokken vanwege onvoldoende schijf ruimte. | U kunt dit probleem oplossen door bestanden op het volume te verplaatsen naar een ander volume, de grootte van het volume te verg Roten of bestanden af te dwingen op laag door gebruik te maken van de Invoke-StorageSyncCloudTiering-cmdlet. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt
Gelaagde bestanden op een server worden ontoegankelijk als de bestanden niet worden ingetrokken voordat een server eindpunt wordt verwijderd.

Fouten die zijn vastgelegd als gelaagde bestanden niet toegankelijk zijn
- Bij het synchroniseren van een bestand wordt fout code-2147942467 (0x80070043-ERROR_BAD_NET_NAME) vastgelegd in het gebeurtenis logboek van ItemResults
- Wanneer u een bestand terugroept, wordt de fout code-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) vastgelegd in het gebeurtenis logboek van RecallResults

Toegang herstellen tot uw gelaagde bestanden is mogelijk als aan de volgende voorwaarden is voldaan:
- Het servereindpunt is verwijderd in de afgelopen 30 dagen
- Het cloudeindpunt is niet verwijderd 
- De bestandsshare is niet verwijderd
- De synchronisatiegroep is niet verwijderd

Als aan de bovenstaande voorwaarden is voldaan, kunt u de toegang tot de bestanden op de server herstellen door binnen 30 dagen het servereindpunt opnieuw te maken op hetzelfde serverpad binnen dezelfde synchronisatiegroep. 

Als niet aan de bovenstaande voorwaarden is voldaan, is het niet mogelijk om de toegang te herstellen, aangezien deze gelaagde bestanden op de server nu zwevend zijn. Volg de onderstaande instructies om de zwevende gelaagde bestanden te verwijderen.

**Opmerkingen**
- Wanneer gelaagde bestanden niet toegankelijk zijn op de server, moet het volledige bestand nog steeds toegankelijk zijn als u rechtstreeks toegang hebt tot de Azure-bestands share.
- Als u zwevende gelaagde bestanden in de toekomst wilt voor komen, volgt u de stappen in [een server eindpunt verwijderen](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) bij het verwijderen van een server eindpunt.

<a id="get-orphaned"></a>**De lijst met zwevende gelaagde bestanden ophalen** 

1. Controleer of Azure File Sync agent versie v 5.1 of hoger is geïnstalleerd.
2. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sla het OrphanTieredFiles.txt uitvoer bestand op in case bestanden moeten worden hersteld na het verwijderen van de back-up.

<a id="remove-orphaned"></a>**Zwevende gelaagde bestanden verwijderen** 

*Optie 1: de zwevende gelaagde bestanden verwijderen*

Met deze optie worden de zwevende gelaagde bestanden op de Windows-Server verwijderd, maar moet het server eindpunt worden verwijderd als dit bestaat als gevolg van recreatie na 30 dagen of is verbonden met een andere synchronisatie groep. Er treden bestands conflicten op als bestanden worden bijgewerkt op de Windows-Server of de Azure-bestands share voordat het server-eind punt opnieuw wordt gemaakt.

1. Controleer of Azure File Sync agent versie v 5.1 of hoger is geïnstalleerd.
2. Back-up maken van de Azure-bestands share en de locatie van het server eindpunt.
3. Verwijder het server eindpunt in de synchronisatie groep (indien aanwezig) door de stappen te volgen die worden beschreven in [een server eindpunt verwijderen](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Als het server-eind punt niet is verwijderd voordat u de cmdlet Remove-StorageSyncOrphanedTieredFiles gebruikt, wordt het volledige bestand in de Azure-bestands share verwijderd als u het zwevende gelaagte bestand op de server verwijdert. 

4. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Sla het OrphanTieredFiles.txt uitvoer bestand op in case bestanden moeten worden hersteld na het verwijderen van de back-up.
6. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden te verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Opmerkingen** 
- Gelaagde bestanden die op de server zijn gewijzigd en niet zijn gesynchroniseerd met de Azure-bestands share, worden verwijderd.
- Gelaagde bestanden die toegankelijk zijn (niet zwevend), worden niet verwijderd.
- Niet-gelaagde bestanden blijven op de server.

7. Optioneel: Maak het server eindpunt opnieuw als dit is verwijderd in stap 3.

*Optie 2: koppel de Azure-bestands share en kopieer de bestanden die zich lokaal op de server bevinden*

Voor deze optie moet het server eindpunt niet worden verwijderd, maar er is voldoende schijf ruimte nodig om de volledige bestanden lokaal te kopiëren.

1. [Koppel](./storage-how-to-use-files-windows.md) de Azure-bestands share op de Windows-Server met zwevende gelaagde bestanden.
2. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Gebruik het OrphanTieredFiles.txt uitvoer bestand om zwevende gelaagde bestanden op de server te identificeren.
4. De zwevende gelaagde bestanden overschrijven door het volledige bestand van de Azure-bestands share te kopiëren naar de Windows-Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Problemen oplossen met bestanden die onverwacht worden ingetrokken op een server  
Anti virus-, back-up-en andere toepassingen die grote aantallen bestanden lezen, veroorzaken onbedoelde aanroepen, tenzij ze het kenmerk offline overs Laan respecteren en het lezen van de inhoud van die bestanden overs Laan. Als u offlinebestanden overslaat voor producten die ondersteuning bieden voor deze optie, voorkomt u dat er onbedoeld wordt teruggehaald tijdens bijvoorbeeld antivirusscans en back-uptaken.

Vraag uw softwareleverancier hoe de oplossing kan worden geconfigureerd zodat offlinebestanden niet meer worden gelezen.

Onbedoelde oproepen kunnen ook in andere scenario's optreden, zoals wanneer u bestanden bekijkt in Verkenner. Als u in Verkenner op de server een map opent met bestanden in cloudlagen, kan dat resulteren in onbedoeld terughalen. Dit is nog waarschijnlijker als er een antivirusoplossing is ingeschakeld op de server.

> [!NOTE]
>Gebruik gebeurtenis-ID 9059 in het logboek voor telemetrie om te bepalen welke toepassing wordt gebruikt voor het terughalen. Deze gebeurtenis voorziet in het terughalen van toepassingen voor een server eindpunt en wordt eenmaal per uur geregistreerd.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1,2 vereist voor Azure File Sync

U kunt de TLS-instellingen op uw server weer geven door de [register instellingen](/windows-server/security/tls/tls-registry-settings)te bekijken. 

Als u een proxy gebruikt, raadpleegt u de documentatie van uw proxy en zorgt u ervoor dat deze is geconfigureerd voor het gebruik van TLS 1.2.

## <a name="general-troubleshooting"></a>Algemene probleem oplossing
Als u problemen ondervindt met Azure File Sync op een server, moet u eerst de volgende stappen uitvoeren:
1. In Logboeken raadpleegt u de logboeken met betrekking tot telemetrie, operationele en diagnose.
    - Problemen met synchronisatie, lagen en intrekken worden vastgelegd in de telemetrie-, diagnostische en operationele gebeurtenis Logboeken onder toepassingen en Services\Microsoft\FileSync\Agent.
    - Problemen met betrekking tot het beheren van een server (bijvoorbeeld configuratie-instellingen) worden vastgelegd in de operationele en diagnostische gebeurtenis Logboeken onder toepassingen en Services\Microsoft\FileSync\Management.
2. Controleer of de Azure File Sync-Service wordt uitgevoerd op de server:
    - Open de MMC-module Services en controleer of de opslag synchronisatie Agent-service (FileSyncSvc) wordt uitgevoerd.
3. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync.sys en StorageSyncGuard.sys) worden uitgevoerd:
    - Voer uit vanaf een opdracht prompt met verhoogde bevoegdheid `fltmc` . Controleer of de StorageSync.sys en StorageSyncGuard.sys Stuur Programma's voor het bestands systeem worden weer gegeven.

Als het probleem niet is opgelost, voert u het hulp programma AFSDiag uit en verzendt u de zip-bestands uitvoer naar de ondersteunings technicus die aan uw aanvraag is toegewezen voor verdere diagnose.

Voer de onderstaande stappen uit om AFSDiag uit te voeren.

Voor Agent versie V11 en hoger:
1. Open een Power shell-venster met verhoogde bevoegdheden en voer de volgende opdrachten uit (druk na elke opdracht op ENTER):

    > [!NOTE]
    >AFSDiag maakt de uitvoermap en een tijdelijke map daarin voordat de logboeken worden verzameld en verwijdert de tijdelijke map na de uitvoering. Geef een uitvoer locatie op die geen gegevens bevat.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reproduceer het probleem. Wanneer u klaar bent, voert u **D** in.
3. Een zip-bestand dat Logboeken en tracerings bestanden bevat, wordt opgeslagen in de uitvoermap die u hebt opgegeven. 

Voor Agent versie V10 toevoegen en eerder:
1. Maak een map waar de uitvoer van de AFSDiag wordt opgeslagen (bijvoorbeeld C:\Output).
    > [!NOTE]
    >AFSDiag verwijdert alle inhoud in de uitvoermap voordat logboeken worden verzameld. Geef een uitvoer locatie op die geen gegevens bevat.
2. Open een Power shell-venster met verhoogde bevoegdheden en voer de volgende opdrachten uit (druk na elke opdracht op ENTER):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Voer voor het traceer niveau van de Azure File Sync-kernelmodus **1** in (tenzij anders opgegeven, om meer uitgebreide traceringen te maken) en druk vervolgens op ENTER.
4. Voor de Azure File Sync traceer niveau van de gebruikers modus voert u **1** in (tenzij anders opgegeven, om meer uitgebreide traceringen te maken) en druk vervolgens op ENTER.
5. Reproduceer het probleem. Wanneer u klaar bent, voert u **D** in.
6. Een zip-bestand dat Logboeken en tracerings bestanden bevat, wordt opgeslagen in de uitvoermap die u hebt opgegeven.


## <a name="see-also"></a>Zie ook
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Veelgestelde vragen over Azure Files](storage-files-faq.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
