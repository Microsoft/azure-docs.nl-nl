---
title: DFS-N gebruiken met Azure Files
description: Algemene DFS-N-gebruiksgevallen met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741776"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>DFS-naamruimten gebruiken met Azure Files
[Naamruimten](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)voor gedistribueerde bestandssystemen , ook wel DFS-naamruimten of DFS-N genoemd, is een Windows Server-serverfunctie die veel wordt gebruikt om de implementatie en het onderhoud van SMB-bestands shares in productie te vereenvoudigen. DFS-naamruimten is een virtualisatietechnologie voor opslagnaamruimten, wat betekent dat u hiermee een laag indirectie kunt bieden tussen het UNC-pad van uw bestands shares en de werkelijke bestands shares zelf. DFS-naamruimten werken met SMB-bestands shares, agnostisch van deze bestands shares worden gehost: het kan worden gebruikt met SMB-shares die worden gehost op een on-premises Windows-bestandsserver met of zonder Azure File Sync, rechtstreeks Azure-bestands shares, SMB-bestands shares die worden gehost in Azure NetApp Files of andere aanbiedingen van derden, en zelfs met bestands shares die worden gehost in andere clouds. 

In de kern biedt DFS-naamruimten een toewijzing tussen een gebruiksvriendelijk UNC-pad, zoals en het onderliggende UNC-pad van de `\\contoso\shares\ProjectX` SMB-share, zoals `\\Server01-Prod\ProjectX` of `\\storageaccount.file.core.windows.net\projectx` . Wanneer eindgebruikers naar hun bestands share willen navigeren, typen ze het gebruiksvriendelijke UNC-pad, maar hun SMB-client heeft toegang tot het onderliggende SMB-pad van de toewijzing. U kunt dit basisconcept ook uitbreiden om een bestaande bestandsservernaam over te nemen, zoals `\\MyServer\ProjectX` . U kunt deze mogelijkheid gebruiken om de volgende scenario's te bereiken:

- Geef een naam op voor migratiebestendige gegevens voor een logische set gegevens. In dit voorbeeld hebt u een toewijzing zoals `\\contoso\shares\Engineering` die wordt toegewezen aan `\\OldServer\Engineering` . Wanneer u de migratie naar Azure Files voltooid, kunt u de toewijzing wijzigen zodat uw gebruiksvriendelijke UNC-pad naar `\\storageaccount.file.core.windows.net\engineering` wijst. Wanneer een eindgebruiker het gebruiksvriendelijke UNC-pad gebruikt, wordt deze naadloos omgeleid naar het pad naar de Azure-bestands share.

- Stel een algemene naam in voor een logische set gegevens die wordt gedistribueerd naar meerdere servers op verschillende fysieke sites, zoals via Azure File Sync. In dit voorbeeld wordt een naam zoals `\\contoso\shares\FileSyncExample` aan meerdere UNC-paden, zoals `\\FileSyncServer1\ExampleShare` , , `\\FileSyncServer2\DifferentShareName` . `\\FileSyncServer3\ExampleShare` Wanneer de gebruiker toegang heeft tot de gebruiksvriendelijke UNC, krijgt deze een lijst met mogelijke UNC-paden en kiest hij het dichtstbijzijnde pad op basis van Windows Server Active Directory(AD)-sitedefinities.

- Breid een logische set gegevens uit over de grootte, I/O of andere schaaldrempels. Dit is gebruikelijk wanneer u te maken hebt met gebruikersmappen, waarbij elke gebruiker een eigen map op een share krijgt, of met scratch-shares, waarbij gebruikers willekeurige ruimte krijgen om tijdelijke gegevensbehoeften te verwerken. Met DFS-naamruimten kunt u meerdere mappen samenbrengen in een samenhangend naamruimte. Wijs bijvoorbeeld `\\contoso\shares\UserShares\user1` toe aan , wordt aan , , en `\\storageaccount.file.core.windows.net\user1` `\\contoso\shares\UserShares\user2` `\\storageaccount.file.core.windows.net\user2` meer.  

In het volgende videooverzicht ziet u een voorbeeld van het gebruik van DFS-naamruimten met uw Azure Files implementatie.

[![Demo over het instellen van DFS-N met Azure Files klik om af te spelen.](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Ga naar 10:10 in de video om te zien hoe u DFS-naamruimten in kunt stellen.

Als u al een DFS-naamruimte hebt, zijn er geen speciale stappen vereist om deze te gebruiken met Azure Files en File Sync. Als u uw Azure-bestands share on-premises wilt openen, zijn normale netwerkoverwegingen van toepassing; zie [Azure Files netwerkoverwegingen](./storage-files-networking-overview.md) voor meer informatie.

## <a name="namespace-types"></a>Naamruimtetypen
DFS-naamruimten biedt twee hoofdnaamruimtetypen:

- **Naamruimte op basis van een domein:** een naamruimte die wordt gehost als onderdeel van Windows Server AD domein. Naamruimten die worden gehost als onderdeel van AD, hebben een UNC-pad met de naam van uw domein, bijvoorbeeld , als `\\contoso.com\shares\myshare` uw domein `contoso.com` is. Naamruimten op basis van een domein bieden ondersteuning voor grotere schaallimieten en ingebouwde redundantie via AD. Naamruimten op basis van een domein kunnen geen geclusterde resource op een failovercluster zijn. 
- **Zelfstandige naamruimte:** een naamruimte die wordt gehost op een afzonderlijke server, die niet wordt gehost als onderdeel van Windows Server AD. Zelfstandige naamruimten hebben een naam op basis van de naam van de zelfstandige server, zoals , waarbij de zelfstandige `\\MyStandaloneServer\shares\myshare` server de naam `MyStandaloneServer` heeft. Zelfstandige naamruimten ondersteunen lagere schaaldoelen dan naamruimten op basis van een domein, maar kunnen worden gehost als een geclusterde resource op een failovercluster.

## <a name="requirements"></a>Vereisten
Als u DFS-naamruimten wilt gebruiken met Azure Files en File Sync, hebt u de volgende resources nodig:

- Een Active Directory-domein. Dit kan overal worden gehost, zoals on-premises, op een virtuele Azure-machine (VM) of zelfs in een andere cloud.
- Een Windows-server die de naamruimte kan hosten. Een veelvoorkomende patroonimplementatiepatroon voor DFS-naamruimten is het gebruik van de Active Directory-domeincontroller voor het hosten van de naamruimten, maar de naamruimten kunnen worden ingesteld vanaf elke server met de serverfunctie DFS-naamruimten geïnstalleerd. DFS-naamruimten zijn beschikbaar op alle ondersteunde Versies van Windows Server.
- Een SMB-bestands share die wordt gehost in een omgeving die lid is van een domein, zoals een Azure-bestands share die wordt gehost in een opslagaccount dat lid is van een domein of een bestands share die wordt gehost op een Windows-bestandsserver die lid is van een domein met behulp van Azure File Sync. Zie Verificatie op basis van identiteit voor meer informatie over het toevoegen van een domein [aan uw opslagaccount.](storage-files-active-directory-overview.md) Windows-bestandsservers zijn op dezelfde manier lid van een domein, ongeacht of u Azure File Sync.
- De SMB-bestands shares die u wilt gebruiken met DFS-naamruimten, moeten bereikbaar zijn vanaf uw on-premises netwerken. Dit is voornamelijk een probleem voor Azure-bestands shares, maar technisch gezien geldt dit voor elke bestands share die wordt gehost in Azure of een andere cloud. Zie Netwerkoverwegingen voor directe toegang [voor meer informatie over netwerken.](storage-files-networking-overview.md)

## <a name="install-the-dfs-namespaces-server-role"></a>De serverfunctie DFS-naamruimten installeren
Als u al DFS-naamruimten gebruikt of DFS-naamruimten wilt instellen op uw domeincontroller, kunt u deze stappen veilig overslaan.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u de serverfunctie DFS-naamruimten wilt installeren, opent u de Serverbeheer op de server. Selecteer **Beheren** en selecteer vervolgens **Functies en onderdelen toevoegen.** De resulterende wizard leidt u door de installatie van de benodigde Windows-onderdelen voor het uitvoeren en beheren van DFS-naamruimten. 

Selecteer in **de sectie Installatietype** van de installatiewizard het keuzerondje **Installatie** op basis van rollen of functies en selecteer **Volgende.** Selecteer in **de sectie** Serverselectie de gewenste server(s) waarop u de serverfunctie DFS-naamruimten wilt installeren en selecteer **Volgende.** 

Selecteer in **de sectie Serverfuncties** de rol **DFS-naamruimten** in de lijst met rollen en schakel deze in. U vindt dit onder **Bestands- en opslagservicesbestand**  >  **en ISCSI-services.** Wanneer u de serverfunctie DFS-naamruimten selecteert, kunnen ook vereiste ondersteunende serverfuncties of onderdelen worden toegevoegd die u nog niet hebt geïnstalleerd.

![Een schermopname van de wizard **Functies en onderdelen toevoegen** met de rol **DFS-naamruimten** geselecteerd.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Nadat u de rol **DFS-naamruimten** hebt  ingeschakeld, kunt u  op alle volgende schermen Volgende selecteren en Installeren selecteren zodra de wizard de knop incheckt. Wanneer de installatie is voltooid, kunt u uw naamruimte configureren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende opdrachten uit vanuit een PowerShell-sessie met verhoogde bevoegdheid (of via extern gebruik van PowerShell).

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Bestaande servernamen overnemen met hoofdconsolidatie
Een belangrijk gebruik voor DFS-naamruimten is het overnemen van een bestaande servernaam om de fysieke indeling van de bestands shares te herbouwen. U kunt bijvoorbeeld bestands shares van meerdere oude bestandsservers samenvoegen op één bestandsserver tijdens een moderniseringsmigratie. Traditioneel beperken de bekendheid van eindgebruikers en documentkoppelingen de mogelijkheid om bestands shares van verschillende bestandsservers op één host te consolideren, maar met de hoofdconsolidatiefunctie DFS-naamruimten kunt u één server samenvoegen tot meerdere servernamen en naar de juiste sharenaam doorverdelen.

Hoewel dit handig is voor verschillende scenario's voor datacentermigratie, is hoofdconsolidatie vooral nuttig voor de overstap op cloudeigen Azure-bestands shares, omdat:

- Met Azure-bestands shares kunt u bestaande on-premises servernamen niet behouden.
- Azure-bestands shares moeten worden gebruikt via de FQDN (Fully Qualified Domain Name) van het opslagaccount. Een Azure-bestands share met de naam `share` in een opslagaccount `storageaccount` is bijvoorbeeld altijd toegankelijk via het `\\storageaccount.file.core.windows.net\share` UNC-pad. Dit kan verwarrend zijn voor eindgebruikers die een korte naam verwachten (bijvoorbeeld `\\MyServer\share`) of een naam die een subdomein is van de domeinnaam van de organisatie (bijvoorbeeld `\\MyServer.contoso.com\share`).

Hoofdconsolidatie kan alleen worden gebruikt met zelfstandige naamruimten. Als u al een bestaande naamruimte op basis van een domein voor uw bestands shares hebt, hoeft u geen geconsolideerde hoofdnaamruimte te maken.

### <a name="enabling-root-consolidation"></a>Basisconsolidatie inschakelen
Hoofdconsolidatie kan worden ingeschakeld door de volgende registersleutels in te stellen vanuit een PowerShell-sessie met verhoogde bevoegdheid (of door gebruik te maken van extern gebruik van PowerShell).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>DNS-vermeldingen maken voor bestaande bestandsservernamen
Als u wilt dat DFS-naamruimten reageren op bestaande bestandsservernamen, maakt u aliasrecords (CNAME) voor uw bestaande bestandsservers die naar de naam van de DFS-naamruimtenserver wijzen. De exacte procedure voor het bijwerken van uw DNS-records kan afhankelijk zijn van de servers die uw organisatie gebruikt en of uw organisatie aangepaste hulpprogramma's gebruikt om het beheer van DNS te automatiseren. De volgende stappen worden weergegeven voor de DNS-server die is opgenomen in Windows Server en automatisch wordt gebruikt door Windows AD.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Open de DNS-beheerconsole op een Windows DNS-server. U vindt dit door de knop **Start** te selecteren en DNS te **typen.** Navigeer naar de zone voor forward lookup voor uw domein. Als uw domein bijvoorbeeld is, kunt u de zone voor forward lookup vinden onder Zones voor forward `contoso.com` **lookup**  >  **`contoso.com`** in de beheerconsole. De exacte hiërarchie die in dit dialoogvenster wordt weergegeven, is afhankelijk van de DNS-configuratie voor uw netwerk.

Klik met de rechtermuisknop op de zone voor forward lookup en selecteer **Nieuwe alias (CNAME)**. Voer in het dialoogvenster dat wordt weergegeven de korte naam in voor de bestandsserver die u vervangt (de volledig gekwalificeerde domeinnaam wordt automatisch ingevuld in het tekstvak met het label **Fully Qualified Domain Name).** Voer in het tekstvak Fully **Qualified Domain Name (FQDN)** voor de doelhost de naam in van de DFS-N-server die u hebt ingesteld. U kunt de knop **Bladeren** gebruiken om de server te selecteren, indien gewenst. Selecteer **OK** om de CNAME-record voor uw server te maken.

![Een schermopname van de **Nieuwe bronrecord** voor een CNAME DNS-vermelding.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Open op een Windows DNS-server een PowerShell-sessie (of gebruik Extern extern gebruik van PowerShell) om de volgende opdrachten uit te voeren, waarbij u en vult met de relevante waarden voor uw omgeving ( wordt automatisch ingevuld met de domeinnaam, maar u kunt dit ook handmatig `$oldServer` `$dfsnServer` `$domain` typen).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Een naamruimte maken
De basiseenheid voor beheer voor DFS-naamruimten is de naamruimte. De hoofdmap van de naamruimte, of naam, is het beginpunt van de naamruimte, zodat in het UNC-pad de hoofdmap van `\\contoso.com\Public\` de naamruimte `Public` is. 

Als u DFS-naamruimten gebruikt om een bestaande servernaam met hoofdconsolidatie over te nemen, moet de naam van de naamruimte de naam zijn van de servernaam die u wilt overnemen, die wordt voorbereid met het teken `#` . Als u bijvoorbeeld een bestaande server met de naam wilt overnemen, maakt u een `MyServer` DFS-N-naamruimte met de naam `#MyServer` . In de onderstaande PowerShell-sectie wordt de vooraf schreven, maar als u maakt via de DFS-beheerconsole, moet u waar nodig de `#` voorbereiding uitvoeren. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een nieuwe naamruimte wilt maken, opent u **de DFS-beheerconsole.** U vindt dit door de knop **Start** te selecteren en **DFS Management te typen.** De resulterende beheerconsole heeft twee **secties Naamruimten** en **Replicatie,** die verwijzen naar respectievelijk DFS-naamruimten en DSF-replicatie (DFS-R). Azure File Sync biedt een modern replicatie- en synchronisatiemechanisme dat kan worden gebruikt in plaats van DFS-R als replicatie ook gewenst is.

Selecteer de **sectie Naamruimten** en selecteer de knop **Nieuwe** naamruimte (u kunt ook met de rechtermuisknop op de sectie **Naamruimten klikken).** De **resulterende wizard Nieuwe naamruimte** leidt u door het maken van een naamruimte. 

Voor de eerste sectie in de wizard moet u de DFS-naamruimteserver kiezen om de naamruimte te hosten. Meerdere servers kunnen een naamruimte hosten, maar u moet DFS-naamruimten instellen met één server tegelijk. Voer de naam van de gewenste DFS-naamruimteserver in en selecteer **Volgende.** In de **sectie Naamruimtenaam en** instellingen kunt u de gewenste naam van uw naamruimte invoeren en Volgende **selecteren.** 

In **de sectie Naamruimtetype** kunt  u kiezen tussen een domeinnaamruimte op basis van een domein en **een zelf-eigen naamruimte**. Als u DFS-naamruimten wilt gebruiken om een bestaande bestandsserver/NAS-apparaatnaam te behouden, moet u de optie voor de zelfstandige naamruimte selecteren. Voor andere scenario's moet u een naamruimte op basis van een domein selecteren. Raadpleeg de [bovenstaande naamruimtetypen](#namespace-types) voor meer informatie over het kiezen tussen naamruimtetypen.

![Een schermopname van het selecteren tussen een domeingebaseerde naamruimte en een zelfstandige naamruimte in de wizard **Nieuwe naamruimte**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Selecteer het gewenste naamruimtetype voor uw omgeving en selecteer **Volgende.** De wizard geeft vervolgens een overzicht van de naamruimte die moet worden gemaakt. Selecteer **Maken om** de naamruimte te maken en Sluiten **wanneer** het dialoogvenster is voltooid.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer vanuit een PowerShell-sessie op de DFS-naamruimteserver de volgende PowerShell-opdrachten uit, en vul , en in met de relevante waarden `$namespace` `$type` voor uw `$takeOverName` omgeving.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Mappen en mapdoelen configureren
Een naamruimte is alleen nuttig als deze mappen en mapdoelen heeft. Elke map kan een of meer mapdoelen hebben. Dit zijn aanwijzers naar de SMB-bestands share(s) die die inhoud hosten. Wanneer gebruikers door een map met mapdoelen bladeren, ontvangt de clientcomputer een verwijzing die de clientcomputer transparant omleiden naar een van de mapdoelen. U kunt ook mappen zonder mapdoelen hebben om structuur en hiërarchie toe te voegen aan de naamruimte.

U kunt DFS-naamruimten zien als mappen die vergelijkbaar zijn met bestands shares. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Selecteer in de DFS-beheerconsole de naamruimte die u zojuist hebt gemaakt en selecteer **Nieuwe map.** In het **dialoogvenster Nieuwe** map kunt u zowel de map als de doelen ervan maken.

![Een schermopname van het dialoogvenster **Nieuwe map**.](./media/files-manage-namespaces/dfs-folder-targets.png)

Geef in het tekstvak met **het label Naam** de naam van de map op. Selecteer **Toevoegen... om** mapdoelen voor deze map toe te voegen. Het **resulterende dialoogvenster Mapdoel** toevoegen bevat een tekstvak met het label **Pad** naar mapdoel waar u het UNC-pad naar de gewenste map kunt op geven. Selecteer **OK** in het **dialoogvenster Mapdoel** toevoegen. Als u een UNC-pad toevoegt aan een Azure-bestands share, ontvangt u mogelijk een bericht met de melding dat de server `storageaccount.file.core.windows.net` geen contactpersonen kan zijn. Dit is verwacht; Selecteer **Ja om** door te gaan. Selecteer ten slotte **OK in** het dialoogvenster **Nieuwe map** om de map- en mapdoelen te maken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Nu u een naamruimte, een map en een mapdoel hebt gemaakt, kunt u uw bestands share via DFS-naamruimten aan de share toevoegen. Als u een naamruimte op basis van een domein gebruikt, moet het volledige pad voor uw share `\\<domain-name>\<namespace>\<share>` zijn. Als u een zelfstandige naamruimte gebruikt, moet het volledige pad voor uw share `\\<DFS-server>\<namespace>\<share>` zijn. Als u een zelfstandige naamruimte met hoofdconsolidatie gebruikt, hebt u rechtstreeks toegang via uw oude servernaam, zoals `\\<old-server>\<share>` .

## <a name="see-also"></a>Zie ook
- Een Azure-bestands share implementeren: [Planning voor](storage-files-planning.md) een Azure Files implementatie en How to create an file share (Een [bestands share maken).](storage-how-to-create-file-share.md)
- Toegang tot bestands share configureren: [Verificatie op basis](storage-files-active-directory-overview.md) van identiteit en [Netwerkoverwegingen voor directe toegang.](storage-files-networking-overview.md)
- [Windows Server Distributed File System-naamruimten](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)