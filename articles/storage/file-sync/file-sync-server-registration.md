---
title: Geregistreerde servers beheren met Azure File Sync | Microsoft Docs
description: Meer informatie over het registreren en ongedaan maken van de registratie van een Windows-server Azure File Sync een opslagsynchronisatieservice.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a85fb653636333beec5f53912a646b3e5619e37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796752"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Geregistreerde servers beheren met Azure File Sync
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit doet u door uw Windows-servers om te zetten in een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

In het volgende artikel wordt beschreven hoe u een server registreert en beheert met een opslagsynchronisatieservice. Zie [Implementatie van Azure File Sync](file-sync-deployment-guide.md) voor meer informatie over het implementeren Azure File Sync end-to-end.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Een server registreren of de registratie ongedaan maken bij opslagsynchronisatieservice
Als u een server registreert bij Azure File Sync brengt u een vertrouwensrelatie tot leven tussen Windows Server en Azure. Deze relatie kan vervolgens  worden gebruikt voor het maken van server-eindpunten op de server, die specifieke mappen vertegenwoordigen die moeten worden gesynchroniseerd met een Azure-bestands share (ook wel een *cloud-eindpunt genoemd).* 

### <a name="prerequisites"></a>Vereisten
Als u een server wilt registreren bij een opslagsynchronisatieservice, moet u eerst uw server voorbereiden met de vereiste vereisten:

* Op uw server moet een ondersteunde versie van Windows Server worden uitgevoerd. Zie systeemvereisten en [interoperabiliteit Azure File Sync meer informatie.](file-sync-planning.md#windows-file-server-considerations)
* Zorg ervoor dat er een opslagsynchronisatieservice is geïmplementeerd. Zie How to deploy Azure File Sync (Een opslagsynchronisatieservice implementeren) voor meer informatie [over het implementeren Azure File Sync.](file-sync-deployment-guide.md)
* Zorg ervoor dat de server is verbonden met internet en dat Azure toegankelijk is.
* Schakel verbeterde beveiliging van IE uit voor beheerders met de Serverbeheer ui.
    
    ![Serverbeheer gebruikersinterface met verbeterde beveiliging van IE gemarkeerd](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Zorg ervoor dat Azure PowerShell-module op uw server is geïnstalleerd. Als uw server lid is van een failovercluster, is voor elk knooppunt in het cluster de Az-module vereist. Meer informatie over het installeren van de Az-module vindt u op De module [installeren en configureren Azure PowerShell.](/powershell/azure/install-Az-ps)

    > [!Note]  
    > U wordt aangeraden de nieuwste versie van de Az PowerShell-module te gebruiken om een server te registreren of de registratie op te maken. Als het Az-pakket eerder op deze server is geïnstalleerd (en de PowerShell-versie op deze server 5.* of hoger is), kunt u de cmdlet gebruiken om dit pakket bij te `Update-Module` werken. 
* Als u een netwerkproxyserver in uw omgeving gebruikt, configureert u proxyinstellingen op uw server om de synchronisatieagent te laten gebruiken.
    1. Uw proxy-IP-adres en poortnummer bepalen
    2. Bewerk deze twee bestanden:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Voeg de regels in afbeelding 1 (onder deze sectie) toe onder /System.ServiceModel in de bovenstaande twee bestanden die 127.0.0.1:8888 wijzigen in het juiste IP-adres (vervang 127.0.0.1) en het juiste poortnummer (vervang 8888):
    4. Stel de WinHTTP-proxyinstellingen in via de opdrachtregel:
        * De proxy tonen: netsh winhttp show proxy
        * Stel de proxy in: netsh winhttp set proxy 127.0.0.1:8888
        * De proxy opnieuw instellen: netsh winhttp reset proxy
        * als dit is ingesteld nadat de agent is geïnstalleerd, start u de synchronisatieagent opnieuw op: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Een server registreren bij opslagsynchronisatieservice
Voordat een server kan worden gebruikt als *een server-eindpunt* in een Azure File Sync synchronisatiegroep *,* moet deze worden geregistreerd bij een *opslagsynchronisatieservice.* Een server kan slechts bij één opslagsynchronisatieservice tegelijk worden geregistreerd.

#### <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
1. [Download de Azure File Sync agent](https://go.microsoft.com/fwlink/?linkid=858257).
2. Start het installatieprogramma Azure File Sync agent.
    
    ![Het eerste deelvenster van het installatieprogramma Azure File Sync agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Zorg ervoor dat u updates voor de Azure File Sync agent inschakelen met behulp Microsoft Update. Het is belangrijk omdat kritieke beveiligingscorrecties en functieverbeteringen voor het serverpakket worden verzonden via Microsoft Update.

    ![Zorg Microsoft Update is ingeschakeld in het deelvenster Microsoft Update van het installatieprogramma Azure File Sync agent](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Als de server niet eerder is geregistreerd, wordt de gebruikersinterface voor serverregistratie onmiddellijk na het voltooien van de installatie beschikbaar.

> [!Important]  
> Als de server lid is van een failovercluster, moet Azure File Sync agent worden geïnstalleerd op elk knooppunt in het cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>De server registreren met behulp van de gebruikersinterface voor serverregistratie
1. Als de gebruikersinterface voor serverregistratie niet onmiddellijk na het voltooien van de installatie van de Azure File Sync-agent is gestart, kan deze handmatig worden gestart door uit te `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` voeren.
2. Klik *op Aanmelden om toegang* te krijgen tot uw Azure-abonnement. 

    ![Dialoogvenster openen van de gebruikersinterface voor serverregistratie](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Kies in het dialoogvenster het juiste abonnement, de juiste resourcegroep en opslagsynchronisatieservice.

    ![Informatie over opslagsynchronisatieservice](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. In de preview-versie is nog een aanmelding vereist om het proces te voltooien. 

    ![Dialoogvenster Aanmelden](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Als de server lid is van een failovercluster, moet op elke server de serverregistratie worden uitgevoerd. Wanneer u de geregistreerde servers in Azure Portal bekijkt, Azure File Sync elk knooppunt automatisch herkend als lid van hetzelfde failovercluster en worden ze op de juiste wijze gegroepeerd.

#### <a name="register-the-server-with-powershell"></a>De server registreren bij PowerShell
U kunt ook serverregistratie uitvoeren via PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>De registratie van de server bij opslagsynchronisatieservice ongedaan maken
Er zijn verschillende stappen vereist om de registratie van een server bij een opslagsynchronisatieservice ongedaan te maken. Laten we eens kijken hoe u de registratie van een server op de juiste manier ongedaan kunt maken.

> [!Warning]  
> Probeer geen problemen met synchronisatie, opslag in cloudlagen of andere aspecten van Azure File Sync op te lossen door de registratie en registratie van een server ongedaan te maken of de server-eindpunten te verwijderen en opnieuw te maken, tenzij dit expliciet wordt geïnstrueerd door een Microsoft-engineer. Het ongedaan maken van de registratie van een server en het verwijderen van server-eindpunten is een destructieve bewerking en gelaagde bestanden op de volumes met server-eindpunten worden niet opnieuw 'verbonden' met hun locaties op de Azure-bestands share nadat de geregistreerde server en server-eindpunten opnieuw zijn gemaakt, wat leidt tot synchronisatiefouten. Houd er ook rekening mee dat gelaagde bestanden die buiten de naamruimte van een server-eindpunt bestaan, permanent verloren kunnen gaan. Gelaagde bestanden kunnen bestaan binnen server-eindpunten, zelfs als opslag in cloudlagen nooit is ingeschakeld.

#### <a name="optional-recall-all-tiered-data"></a>(Optioneel) Alle gelaagde gegevens terughalen
Als u wilt dat bestanden die momenteel gelaagd zijn beschikbaar zijn na het verwijderen van Azure File Sync (dat wil zeggen dat dit een productieomgeving is, geen testomgeving), kunt u alle bestanden op elk volume met server-eindpunten inroepen. Schakel opslag in cloudlagen uit voor alle server-eindpunten en voer vervolgens de volgende PowerShell-cmdlet uit:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Als het lokale volume dat als host voor het server-eindpunt wordt gehost onvoldoende vrije ruimte heeft om alle gelaagde gegevens terug te halen, mislukt `Invoke-StorageSyncFileRecall` de cmdlet.  

#### <a name="remove-the-server-from-all-sync-groups"></a>De server uit alle synchronisatiegroepen verwijderen
Voordat u de registratie van de server op de opslagsynchronisatieservice ongedaan gaat maken, moeten alle server-eindpunten op die server worden verwijderd. U kunt dit doen via de Azure Portal:

1. Navigeer naar de opslagsynchronisatieservice waar uw server is geregistreerd.
2. Verwijder alle server-eindpunten voor deze server in elke synchronisatiegroep in de opslagsynchronisatieservice. Dit kan worden bereikt door met de rechtermuisknop op het relevante server-eindpunt in het deelvenster synchronisatiegroep te klikken.

    ![Een server-eindpunt verwijderen uit een synchronisatiegroep](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Dit kan ook worden bereikt met een eenvoudig PowerShell-script:

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

#### <a name="unregister-the-server"></a>Registratie van de server ongedaan maken
Nu alle gegevens zijn ingetrokken en de server is verwijderd uit alle synchronisatiegroepen, kan de registratie van de server ongedaan worden maken. 

1. Navigeer Azure Portal de sectie Geregistreerde *servers* van de opslagsynchronisatieservice.
2. Klik met de rechtermuisknop op de server die u wilt de registratie ongedaan maken en klik op 'Registratie van server ongedaan maken'.

    ![Registratie van server ongedaan maken](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Ervoor zorgen Azure File Sync een goede buur in uw datacenter is 
Aangezien Azure File Sync zelden de enige service is die in uw datacenter wordt uitgevoerd, kunt u het netwerk- en opslaggebruik van uw Azure File Sync.

> [!Important]  
> Het instellen van limieten te laag is van invloed op de prestaties Azure File Sync synchronisatie en terugroepen.

### <a name="set-azure-file-sync-network-limits"></a>Netwerklimieten Azure File Sync instellen
U kunt het netwerkgebruik van de Azure File Sync met behulp van `StorageSyncNetworkLimit` de cmdlets.

> [!Note]  
> Netwerklimieten zijn niet van toepassing wanneer een gelaagd bestand wordt gebruikt.

U kunt bijvoorbeeld een nieuwe beperkingslimiet maken om ervoor te zorgen dat Azure File Sync niet meer dan 10 Mbps gebruikt tussen 9.00 en 17.00 uur (17:00 uur) tijdens de werkweek: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

U kunt uw limiet zien met behulp van de volgende cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Gebruik om netwerklimieten te `Remove-StorageSyncNetworkLimit` verwijderen. Met de volgende opdracht worden bijvoorbeeld alle netwerklimieten verwijderd:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>QoS voor Windows Server-opslag gebruiken 
Wanneer Azure File Sync wordt gehost op een virtuele machine die wordt uitgevoerd op een Windows Server-virtualisatiehost, kunt u QoS voor opslag (quality of service voor opslag) gebruiken om het IO-verbruik van opslag te reguleren. Het QoS-beleid voor opslag kan worden ingesteld als een maximum (of limiet, zoals hoe de limiet voor StorageSyncNetwork hierboven wordt afgedwongen) of als een minimum (of reservering). Door een minimum in plaats van een maximum in te stellen, Azure File Sync burst de beschikbare opslagbandbreedte gebruiken als andere workloads deze niet gebruiken. Zie Storage Quality of Service voor [meer informatie.](/windows-server/storage/storage-qos/storage-qos-overview)

## <a name="see-also"></a>Zie ook
- [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
- [Azure Files SYNC implementeren](file-sync-deployment-guide.md)
- [Azure File Sync bewaken](file-sync-monitoring.md)
- [Problemen oplossen met Azure File Sync](file-sync-troubleshoot.md)