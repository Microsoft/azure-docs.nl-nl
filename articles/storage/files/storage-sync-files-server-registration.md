---
title: Geregistreerde servers beheren met Azure File Sync | Microsoft Docs
description: Meer informatie over het registreren en opheffen van de registratie van een Windows-Server met een Azure File Sync Storage-synchronisatie service.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013918"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Geregistreerde servers beheren met Azure File Sync
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit doet u door uw Windows-servers te transformeren naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

In het volgende artikel ziet u hoe u een server met een opslag synchronisatie service registreert en beheert. Zie [Azure file sync implementeren](storage-sync-files-deployment-guide.md) voor informatie over het implementeren van Azure File Sync End-to-end.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Een server bij de opslag synchronisatie service registreren of de registratie ervan opheffen
Bij het registreren van een server met Azure File Sync wordt een vertrouwens relatie tussen Windows Server en Azure tot stand gebracht. Deze relatie kan vervolgens worden gebruikt om *Server eindpunten* te maken op de server, die specifieke mappen vertegenwoordigen die moeten worden gesynchroniseerd met een Azure-bestands share (ook wel een *Cloud-eind punt* genoemd). 

### <a name="prerequisites"></a>Vereisten
Als u een server wilt registreren bij een opslag synchronisatie service, moet u eerst uw server voorbereiden met de vereiste onderdelen:

* Op de server moet een ondersteunde versie van Windows Server worden uitgevoerd. Zie [Azure File Sync System Requirements and interoperabiliteit](storage-sync-files-planning.md#windows-file-server-considerations)(Engelstalig) voor meer informatie.
* Zorg ervoor dat er een opslag synchronisatie service is geïmplementeerd. Zie [Azure file sync implementeren](storage-sync-files-deployment-guide.md)voor meer informatie over het implementeren van een opslag synchronisatie service.
* Zorg ervoor dat de server is verbonden met internet en dat Azure toegankelijk is.
* Schakel de verbeterde beveiliging van Internet Explorer voor beheerders uit met de gebruikers interface van Serverbeheer.
    
    ![Serverbeheer gebruikers interface met de verbeterde beveiliging van Internet Explorer gemarkeerd](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Zorg ervoor dat de module Azure PowerShell is geïnstalleerd op uw server. Als uw server lid is van een failovercluster, moet op elk knoop punt in het cluster de module AZ worden vereist. Meer informatie over het installeren van de AZ-module vindt u in de [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps).

    > [!Note]  
    > U kunt het beste de nieuwste versie van de AZ Power shell-module gebruiken om een server te registreren of de registratie ervan ongedaan te maken. Als het pakket AZ eerder op deze server is geïnstalleerd (en de Power shell-versie op deze server 5. * of hoger is), kunt u de `Update-Module` cmdlet gebruiken om dit pakket bij te werken. 
* Als u een netwerk proxy server in uw omgeving gebruikt, configureert u de proxy-instellingen op uw server zodat de synchronisatie agent kan gebruiken.
    1. Het IP-adres en poort nummer van uw proxy bepalen
    2. Bewerk deze twee bestanden:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Voeg de regels in afbeelding 1 (onder deze sectie) onder/System.ServiceModel in de bovenstaande twee bestanden die 127.0.0.1:8888 wijzigen in het juiste IP-adres (vervangen 127.0.0.1) en het juiste poort nummer (vervangen 8888):
    4. De WinHTTP-proxy-instellingen instellen via de opdracht regel:
        * De proxy weer geven: netsh WinHTTP show proxy
        * Stel de proxy in: netsh WinHTTP set proxy 127.0.0.1:8888
        * De proxy opnieuw instellen: netsh WinHTTP reset proxy
        * Als dit het geval is nadat de agent is geïnstalleerd, start u de synchronisatie agent opnieuw: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Een server met de opslag synchronisatie service registreren
Voordat een server kan worden gebruikt als een *Server eindpunt* in een Azure file sync *synchronisatie groep*, moet deze zijn geregistreerd bij een *opslag synchronisatie service*. Een server kan slechts met één opslag synchronisatie service tegelijk worden geregistreerd.

#### <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
1. [Down load de Azure file sync-agent](https://go.microsoft.com/fwlink/?linkid=858257).
2. Start het installatie programma van de Azure File Sync-agent.
    
    ![Het eerste deel venster van het installatie programma van de Azure File Sync-agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Zorg ervoor dat u updates voor de Azure File Sync agent inschakelt met behulp van Microsoft Update. Het is belang rijk omdat essentiële beveiligingsfixes en functie verbeteringen voor het server pakket worden verzonden via Microsoft Update.

    ![Zorg ervoor dat Microsoft Update is ingeschakeld in het deel venster Microsoft Update van het installatie programma van Azure File Sync agent](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Als de server nog niet eerder is geregistreerd, wordt de gebruikers interface voor Server registratie onmiddellijk weer gegeven nadat de installatie is voltooid.

> [!Important]  
> Als de server lid is van een failovercluster, moet de Azure File Sync-agent worden geïnstalleerd op elk knoop punt in het cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>De server registreren met de gebruikers interface voor Server registratie
1. Als de gebruikers interface voor Server registratie niet onmiddellijk is gestart na het volt ooien van de installatie van de Azure File Sync agent, kunt u deze hand matig starten door uit te voeren `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Klik op *Aanmelden* om toegang te krijgen tot uw Azure-abonnement. 

    ![Dialoog venster openen van de gebruikers interface voor Server registratie](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Kies het juiste abonnement, de resource groep en de opslag synchronisatie service in het dialoog venster.

    ![Gegevens van de opslag synchronisatie service](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. In de preview-versie is een meer aanmelding vereist om het proces te volt ooien. 

    ![Dialoog venster Aanmelden](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Als de server lid is van een failovercluster, moet elke server de server registratie uitvoeren. Wanneer u de geregistreerde servers in azure Portal bekijkt, herkent Azure File Sync elk knoop punt automatisch als lid van hetzelfde failovercluster en worden ze op de juiste wijze gegroepeerd.

#### <a name="register-the-server-with-powershell"></a>De server registreren bij Power shell
U kunt ook Server registratie uitvoeren via Power shell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>De registratie van de server bij de opslag synchronisatie service ongedaan maken
Er zijn verschillende stappen vereist voor het opheffen van de registratie van een server bij een opslag synchronisatie service. Laten we eens kijken hoe u de registratie van een server goed kunt opheffen.

> [!Warning]  
> Probeer geen problemen met synchronisatie, Cloud lagen of enig ander aspect van Azure File Sync op te lossen door de registratie van een server ongedaan te maken en te registreren, of door de server eindpunten te verwijderen en opnieuw te maken, tenzij expliciet door een micro soft-Engineer wordt geïnstrueerd. Het ongedaan maken van de registratie van een server en het verwijderen van server eindpunten is een destructieve bewerking, en gelaagde bestanden op de volumes met server-eind punten worden niet opnieuw verbonden met hun locaties op de Azure-bestands share nadat de geregistreerde server en de server eindpunten opnieuw zijn gemaakt. Dit leidt tot synchronisatie fouten. Opmerking: gelaagde bestanden die zich buiten een naam ruimte van een server eindpunt bevinden, kunnen permanent verloren gaan. Gelaagde bestanden kunnen bestaan in Server-eind punten, zelfs als Cloud lagen nooit zijn ingeschakeld.

#### <a name="optional-recall-all-tiered-data"></a>Beschrijving Alle gelaagde gegevens intrekken
Als u wilt dat bestanden die momenteel zijn gelaagd, beschikbaar zijn na het verwijderen van Azure File Sync (dat wil zeggen een productie, geen test omgeving), worden alle bestanden op elk volume met server eindpunten ingetrokken. Schakel Cloud lagen voor alle server eindpunten uit en voer vervolgens de volgende Power shell-cmdlet uit:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Als het lokale volume dat als host fungeert voor het server eindpunt onvoldoende beschik bare ruimte heeft om alle gelaagde gegevens in te trekken, `Invoke-StorageSyncFileRecall` mislukt de cmdlet.  

#### <a name="remove-the-server-from-all-sync-groups"></a>De server uit alle synchronisatie groepen verwijderen
Voordat u de registratie van de server bij de opslag synchronisatie service ongedaan maakt, moeten alle server eindpunten op die server worden verwijderd. Dit kan worden gedaan via de Azure Portal:

1. Navigeer naar de opslag synchronisatie service waar uw server is geregistreerd.
2. Verwijder alle server eindpunten voor deze server in elke synchronisatie groep in de opslag synchronisatie service. U kunt dit doen door met de rechter muisknop te klikken op het relevante server eindpunt in het deel venster synchronisatie groep.

    ![Een server eindpunt verwijderen uit een synchronisatie groep](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Dit kan ook worden bereikt met een eenvoudig Power shell-script:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>De registratie van de server opheffen
Nu alle gegevens zijn ingetrokken en de server is verwijderd uit alle synchronisatie groepen, kan de registratie van de server ongedaan worden gemaakt. 

1. Ga in het Azure Portal naar het gedeelte *geregistreerde servers* van de opslag synchronisatie service.
2. Klik met de rechter muisknop op de server waarvan u de registratie ongedaan wilt maken en klikt u op Server registratie ongedaan maken.

    ![Registratie server opheffen](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zorgen dat Azure File Sync een goede buur is in uw Data Center 
Omdat Azure File Sync zelden als enige service wordt uitgevoerd in uw Data Center, kunt u het netwerk-en opslag gebruik van Azure File Sync beperken.

> [!Important]  
> De instellings limieten te laag zijn van invloed op de prestaties van Azure File Sync synchronisatie en het terughalen.

### <a name="set-azure-file-sync-network-limits"></a>Azure File Sync netwerk limieten instellen
U kunt het netwerk gebruik van Azure File Sync beperken met behulp van de- `StorageSyncNetworkLimit` cmdlets.

> [!Note]  
> Netwerk limieten zijn niet van toepassing wanneer een gelaagd bestand wordt geopend.

U kunt bijvoorbeeld een nieuwe beperkings limiet maken om ervoor te zorgen dat Azure File Sync gedurende de werk week niet meer dan 10 Mbps van 9 uur en 5 pm (17:00h) gebruikt: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

U kunt uw limiet zien met behulp van de volgende cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Als u netwerk limieten wilt verwijderen, gebruikt u `Remove-StorageSyncNetworkLimit` . Met de volgende opdracht worden bijvoorbeeld alle netwerk limieten verwijderd:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>QoS voor Windows Server-opslag gebruiken 
Als Azure File Sync wordt gehost op een virtuele machine die wordt uitgevoerd op een Windows Server Virtualization-Host, kunt u QoS voor opslag (Quality of service voor opslag) gebruiken om opslag-i/o-verbruik te reguleren. Het QoS-beleid voor opslag kan worden ingesteld als een maximum (of limiet, bijvoorbeeld hoe StorageSyncNetwork limiet wordt afgedwongen) of als een minimum (of reserve ring). Als u een minimum instelt in plaats van Maxi maal, kan Azure File Sync burst gebruiken voor het gebruik van beschik bare opslag bandbreedte als andere workloads deze niet gebruiken. Zie [opslag Quality of service](/windows-server/storage/storage-qos/storage-qos-overview)voor meer informatie.

## <a name="see-also"></a>Zie ook
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Problemen oplossen met Azure File Sync](storage-sync-files-troubleshoot.md)