---
title: Een .NET-app in een container implementeren in Azure Service Fabric
description: Informatie over hoe u een bestaande .NET-toepassing in een container plaatst met behulp van Visual Studio en lokaal fouten opspoort in containers in Service Fabric. De in een container geplaatste toepassing wordt naar een Azure-containerregister gepusht en geïmplementeerd in een Service Fabric-cluster. Wanneer de toepassing is geïmplementeerd in Azure, gebruikt deze Azure SQL DB voor het persistent maken van gegevens.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936708"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Zelfstudie: Een .NET-toepassing in een Windows-container implementeren in Azure Service Fabric

Deze zelfstudie laat u zien hoe u een bestaande ASP.NET-toepassing in een container plaatst en verpakt als een Service Fabric-toepassing.  Voer de containers lokaal uit in het Service Fabric-ontwikkelingscluster en implementeer de toepassing vervolgens in Azure.  De toepassing maakt gegevens persistent in [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een bestaande toepassing in een container plaatsen met behulp van Visual Studio
> * Een database maken in Azure SQL Database
> * Een Azure Container Registry maken
> * Een Service Fabric-toepassing implementeren in Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

1. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Schakel Windows **-onderdelen Hyper-V** en **containers** in.
3. Installeer [docker Desktop voor Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) zodat u containers kunt uitvoeren op Windows 10.
4. Installeer [Service Fabric-runtime versie 6.2 of hoger](service-fabric-get-started.md) en de [Service Fabric SDK versie 3.1](service-fabric-get-started.md) of hoger.
5. Installeer [Visual Studio 2019 versie 16.1](https://www.visualstudio.com/) of hoger met de **Azure-ontwikkelworkload** en de **ASP.NET-ontwikkelings- en webontwikkelingsworkloads**.
6. Installeer [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Fabrikam Fiber CallCenter downloaden en uitvoeren

1. Download de voorbeeldtoepassing [Fabrikam Fiber CallCenter][link-fabrikam-github].  Klik op de koppeling **Archief downloaden**.  Extraheer vanuit de map *sourceCode* in het bestand *fabrikam.zip* het bestand *sourceCode.zip* en extraheer de map *VS2015* vervolgens op uw computer.

2. Controleer of de toepassing Fabrikam Fiber CallCenter wordt opgebouwd en zonder fouten wordt uitgevoerd.  Start Visual Studio als een **beheerder** en open het bestand [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Druk op F5 om fouten op te sporen in de toepassing en de toepassing uit te voeren.

   ![Schermopname van de startpagina van de Fabrikam Fiber CallCenter-toepassing die wordt uitgevoerd op de lokale host. Op de pagina wordt een dashboard weergegeven met een lijst met ondersteuningsoproepen.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Een Azure SQL DB maken

Wanneer de toepassing Fabrikam Fiber CallCenter in productie wordt uitgevoerd, moeten de gegevens persistent worden gemaakt in een database. Er is momenteel geen manier om behoud van gegevens in een container te waarborgen, daarom kunt u productiegegevens niet opslaan in SQL Server in een container.

Wij raden [Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md) aan. Als u een beheerde SQL Server-database in Azure wilt instellen en uitvoeren, voert u het volgende script uit.  Wijzig de scriptvariabelen indien nodig. *client-IP* is het IP-adres van uw ontwikkelcomputer. Noteer de naam van de server die door het script wordt weergegeven.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Als u zich achter een bedrijfsfirewall bevindt, is het IP-adres van uw ontwikkelcomputer mogelijk geen IP-adres dat beschikbaar is voor internet. Ter verificatie of de database het juiste IP-adres heeft voor de firewall-regel, gaat u naar de [Azure-portal](https://portal.azure.com) en zoekt u uw database in de sectie SQL Database. Klik op de naam van de database en klik in de sectie Overzicht op Serverfirewall instellen. Client-IP-adres is het IP-adres van uw ontwikkelcomputer. Controleer of dit adres overeenkomt met het IP-adres in de regel AllowClient.

## <a name="update-the-web-config"></a>De webconfiguratie bijwerken

Werk in het project **FabrikamFiber.Web** de verbindingsreeks in het bestand **web.config** bij om te verwijzen naar de SQL Server in de container.  Wijzig het *Server*-gedeelte van de verbindingsreeks in de naam van de server die door het vorige script is gemaakt. Deze moet ongeveer als volgt zijn: fab-fiber-751718376.database.windows.net. In de volgende XML hoeft u alleen het kenmerk `connectionString` bij te werken. De kenmerken `providerName` en `name` hoeven niet te worden gewijzigd.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>U een SQL Server naar keuze gebruiken voor lokale foutopsporing, zolang deze bereikbaar is vanaf de host. **localdb** biedt echter geen ondersteuning voor `container -> host`-communicatie. Als u een andere SQL-database wilt gebruiken tijdens het bouwen van een release-build van uw webtoepassing, voeg dan nog een verbindingsreeks toe aan uw *web.release.config*-bestand.

## <a name="containerize-the-application"></a>De toepassing in een container plaatsen

1. Klik met de rechtermuisknop op het project **FabrikamFiber.Web** > **Add**  > **Container Orchestrator Support**.  Selecteer **Service Fabric** als de containerorchestrator en klik op **OK**.

2. Klik op **Ja** als dat wordt gevraagd om Docker nu over te schakelen naar Windows-containers.

   Het nieuwe Service Fabric-toepassingsproject **FabrikamFiber.CallCenterApplication** wordt in de oplossing gemaakt.  Er wordt een Dockerfile toegevoegd aan het bestaande **FabrikamFiber.Web**-project.  Ook wordt de map **PackageRoot** toegevoegd aan het project **FabrikamFiber.Web**, dat het servicemanifest en de instellingen voor de nieuwe FabrikamFiber.Web-service bevat.

   De container is nu klaar om te worden gebouwd en verpakt in een Service Fabric-toepassing. Zodra u de installatiekopie van de container hebt gebouwd op uw computer, kunt u deze naar een containerregister pushen en naar elke host ophalen om te worden uitgevoerd.

## <a name="run-the-containerized-application-locally"></a>De in een container geplaatste toepassing lokaal uitvoeren

Druk op **F5** om de toepassing in een container uit te voeren en er fouten in op te sporen in het Service Fabric-ontwikkelingscluster. Klik op **Ja** als u een bericht ziet waarin wordt gevraagd om de groep 'ServiceFabricAllowedUsers' lees- en schrijfmachtigingen te verlenen voor de projectmap van Visual Studio.

Als op F5 run een uitzonde ring wordt gegenereerd, zoals hieronder, is het juiste IP-adres niet toegevoegd aan de firewall van de Azure-data base.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Voer de volgende opdracht uit om het juiste IP-adres toe te voegen aan de Azure data base-firewall.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Een containerregister maken

Nu de toepassing lokaal wordt uitgevoerd, begint u met de voorbereidingen voor implementatie in Azure.  Containerinstallatiekopieën moeten worden opgeslagen in een containerregister.  Maak een [Azure Container Registry](../container-registry/container-registry-intro.md) met behulp van het volgende script. De registernaam van de container is zichtbaar voor andere Azure-abonnementen en moet daarom uniek zijn.
Voordat u de toepassing in Azure implementeert, pusht u de containerinstallatiekopie naar dit register.  Wanneer de toepassing in het cluster in Azure is geïmplementeerd, wordt de installatiekopie van de container opgehaald uit dit register.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Een Service Fabric-cluster maken in Azure

Service Fabric-toepassingen worden uitgevoerd op een cluster, een set virtuele of fysieke machines die verbonden zijn over een netwerk.  Voordat u de toepassing in Azure kunt implementeren, moet u een Service Fabric-cluster maken in Azure.

U kunt:

* Een testcluster maken vanuit Visual Studio. Met deze optie kunt u een beveiligd cluster rechtstreeks vanuit Visual Studio met de configuraties van uw voorkeur maken.
* [Een beveiligd cluster maken op basis van een sjabloon](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

In deze zelfstudie wordt een cluster gemaakt vanuit Visual Studio, wat ideaal is voor testscenario's. Als u op een andere manier een cluster hebt gemaakt of een bestaand cluster gebruikt, kunt u uw verbindingseindpunt kopiëren en plakken, of kunt u het kiezen vanuit uw abonnement.

Open project fabrikamfiber. Web-> PackageRoot-> ServiceManifest.xml in de Solution Explorer voordat u begint. Noteer de poort voor de webfront-end die vermeld staat in **Eindpunt**.

Als u het cluster maakt, gaat u als volgt te werk:

1. Klik met de rechtermuisknop op het toepassingsproject **FabrikamFiber.CallCenterApplication** in Solution Explorer en kies **Publiceren**.
2. Meld u aan met behulp van uw Azure-account, zodat u toegang hebt tot uw abonnement(en).
3. Selecteer onder de vervolgkeuzelijst voor het **verbindingseindpunt** de optie **Nieuw cluster maken...** .
4. Wijzig de volgende instellingen in het dialoogvenster **Cluster maken**:

    a. Geef de naam van het cluster op in het veld **Clusternaam**, evenals het abonnement en de locatie die u wilt gebruiken. Noteer de naam van de clusterbrongroep.

    b. Optioneel: U kunt het aantal knooppunten wijzigen. Standaard beschikt u over drie knooppunten, het minimale aantal dat is vereist om Service Fabric-scenario's te kunnen testen.

    c. Selecteer het tabblad **Certificaat**. Typ op dit tabblad een wachtwoord dat u wilt gebruiken om het certificaat van uw cluster te beschermen. Met dit certificaat is uw cluster beter beveiligd. U kunt ook het pad wijzigen waar u het certificaat wilt opslaan. Visual Studio kan het certificaat voor u importeren, aangezien dit een vereiste stap is om de toepassing naar het cluster te kunnen publiceren.

    >[!NOTE]
    >Houd rekening met het pad naar de map waarin dit certificaat is geïmporteerd. De volgende stap na het maken van het cluster is het importeren van dit certificaat.

    d. Selecteer het tabblad **VM-details**. Geef het wachtwoord op dat u wilt gebruiken voor de virtuele machines (VM's) die het cluster vormen. De gebruikersnaam en het wachtwoord kunnen worden gebruikt om een externe verbinding met de virtuele machines tot stand te brengen. U moet ook een VM-machinegrootte selecteren, en u kunt indien nodig de VM-installatiekopie wijzigen.

    > [!IMPORTANT]
    > Kies een SKU die ondersteuning biedt voor actieve containers. Het besturingssysteem Windows Server op de clusterknooppunten moet compatibel zijn met het Windows Server-besturingssysteem van de container. Zie [Compatibiliteit tussen besturingssysteem van Windows Server-container en host-besturingssysteem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) voor meer informatie. In deze zelfstudie wordt standaard een Docker-installatiekopie op basis van Windows Server 2016 LTSC gebruikt. Containers op basis van deze installatiekopie kunnen worden uitgevoerd op clusters die zijn gemaakt met Windows Server 2016 Datacenter with Containers. Als u echter een cluster maakt of een bestaand cluster gebruikt op een andere versie van Windows Server, moet u de installatiekopie van het besturingssysteem wijzigen waarop de container is gebaseerd. Open de **dockerfile** in het project **FabrikamFiber.Web** Voeg een opmerking toe aan een eventuele `FROM`-instructie op basis van een vorige versie van Windows Server en voeg vanaf de [DockerHub-pagina Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore) een `FROM`-instructie toe op basis van de tag van de gewenste versie. Zie [Windows Server Core release info](/windows-server/get-started/windows-server-release-info) (Informatie over de versie van Windows Server Core) voor meer informatie over versies, ondersteuningstijdlijnen en versiebeheer van Windows Server Core. 

    e. Geef op het tabblad **Geavanceerd** de toepassingspoort aan die moet worden geopend in de load balancer wanneer het cluster wordt geïmplementeerd. Dit is de poort die u hebt genoteerd voordat u het cluster ging maken. U kunt ook een bestaande Application Insights-sleutel toevoegen die moet worden gebruikt om logboekbestanden naar door te sturen.

    f. Wanneer u klaar bent met het wijzigen van de instellingen, selecteert u de knop **Maken**.

5. Het maken duurt enkele minuten; in het uitvoervenster wordt aangegeven wanneer het cluster helemaal klaar is.

## <a name="install-the-imported-certificate"></a>Het geïmporteerde certificaat installeren

Installeer het certificaat dat is geïmporteerd als onderdeel van de stap voor het maken van het cluster naar de locatie van de **huidige gebruiker** en geef het wacht woord voor de persoonlijke sleutel op dat u hebt opgegeven.

U kunt de installatie controleren door **gebruikers certificaten beheren** te openen via het configuratie scherm en te bevestigen dat het certificaat is geïnstalleerd onder **certificaten: huidige**  ->  **persoonlijke**  ->  **certificaten** van gebruiker. Het certificaat moet hetzelfde zijn als *[cluster naam]*. *[Cluster locatie]*. cloudapp.Azure.com, bijvoorbeeld *fabrikamfibercallcenter.southcentralus.cloudapp.Azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Toestaan dat uw toepassing die in Azure wordt uitgevoerd toegang heeft tot SQL Database

Eerder hebt u een SQL-firewallregel gemaakt om toegang te verlenen aan uw toepassing die lokaal wordt uitgevoerd.  Nu moet u voor de toepassing die in Azure wordt uitgevoerd toegang inschakelen voor de SQL-database.  Maak een [service-eindpunt voor virtueel netwerk](../azure-sql/database/vnet-service-endpoint-rule-overview.md) voor het Service Fabric-cluster en maak vervolgens een regel om dat eindpunt toegang tot de SQL-database toe te staan. Geef de variabele van de clusterbrongroep op die u hebt genoteerd toen u het cluster maakte.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>De toepassing implementeren in Azure

Nu de toepassing klaar is, kunt u deze rechtstreeks vanuit Visual Studio implementeren naar het cluster in Azure.  Klik in Solution Explorer met de rechtermuisknop op het toepassingsproject **FabrikamFiber.CallCenterApplication** in Solution Explorer en selecteer **Publiceren**.  Selecteer in **Verbindingseindpunt** het eindpunt van het cluster dat u eerder hebt gemaakt.  Selecteer in **Azure Container Registry** het containerregister dat u eerder hebt gemaakt.  Klik op **Publiceren** om de toepassing naar het cluster in Azure te publiceren.

![Uw toepassing publiceren][publish-app]

Volg de voortgang van de implementatie in het uitvoervenster. Wanneer de toepassing is geïmplementeerd, opent u een browser en typt u het clusteradres en de toepassingspoort. Bijvoorbeeld `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Schermopname van de startpagina van de Fabrikam Fiber CallCenter-toepassing die wordt uitgevoerd op azure.com. Op de pagina wordt een dashboard weergegeven met een lijst met ondersteuningsoproepen.][fabrikam-web-page-deployed]

Als de pagina niet kan worden geladen of als er niet wordt gevraagd om het certificaat, opent u het Explorer-pad, bijvoorbeeld `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer` en selecteert u het zojuist geïnstalleerde certificaat.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Continue integratie en implementatie (CI/CD) met een Service Fabric-cluster instellen

Zie voor meer informatie over het gebruik van Azure DevOps om de implementatie van de toepassing van CI/CD in een Service Fabric-cluster te configureren de [Zelfstudie: Een toepassing met CI/CD implementeren in een Service Fabric-cluster](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). De procedure die in de zelfstudie is beschreven is dezelfde voor dit project (FabrikamFiber), maar sla het downloaden van het voorbeeld van de stemtoepassing gewoon over en vervang Voting als de naam van de opslagplaats door FabrikamFiber.

## <a name="clean-up-resources"></a>Resources opschonen

Vergeet als u klaar bent niet om alle resources die u hebt gemaakt te verwijderen.  De eenvoudigste manier hiervoor is om de resourcegroepen te verwijderen die het Service Fabric-cluster, Azure SQL DB en Azure Container Registry bevatten.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Een bestaande toepassing in een container plaatsen met behulp van Visual Studio
> * Een database maken in Azure SQL Database
> * Een Azure Container Registry maken
> * Een Service Fabric-toepassing implementeren in Azure

In het volgende deel van de zelfstudie leert u hoe u [een containertoepassing met CI/CD in een Service Fabric-cluster kunt implementeren](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png