---
title: Beschikbaarheidsgroepen voor SQL Server configureren op virtuele RHEL-machines in Azure | Virtuele Linux-machines | Microsoft Docs
description: Meer informatie over het instellen van hoge beschikbaarheid in een RHEL-clusteromgeving en het instellen van STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 9a6faec2542337eedbe4aafb69f1061582f92cc7
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531561"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelfstudie: Beschikbaarheidsgroepen voor SQL Server configureren op virtuele RHEL-machines in Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> In deze zelfstudie gebruiken we SQL Server 2017 met RHEL 7.6, maar u kunt ook SQL Server 2019 in RHEL 7 of RHEL 8 gebruiken om hoge beschikbaarheid te configureren. De opdrachten voor het configureren van de Pacemaker-cluster en resources van beschikbaarheidsgroep zijn gewijzigd in RHEL 8. In het artikel [Resource voor beschikbaarheidsgroep maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8-resources vindt u meer informatie over de juiste opdrachten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een nieuwe resourcegroep, beschikbaarheidsset en virtuele Linux-machines (VM's) maken
> - Hoge beschikbaarheid inschakelen
> - Een Pacemaker-cluster maken
> - Een fencing-agent configureren door een STONITH-apparaat te maken
> - SQL Server en MSSQL-hulpprogramma's installeren op RHEL
> - AlwaysOn-beschikbaarheidsgroep in SQL Server configureren
> - Resources van de beschikbaarheidsgroep configureren in het Pacemaker-cluster
> - Een failover en de fencing-agent testen

In deze zelfstudie wordt de Azure CLI gebruikt om resources in Azure te implementeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u de CLI liever lokaal installeert en gebruikt, hebt u voor deze zelfstudie Azure CLI versie 2.0.30 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u meerdere abonnementen hebt, [stelt u het abonnement in](/cli/azure/manage-azure-subscriptions-azure-cli) waarvoor u deze resources wilt implementeren.

Gebruik de volgende opdracht om een resourcegroep `<resourceGroupName>` in een regio te maken. Vervang `<resourceGroupName>` door een naam van uw keuze. Voor deze zelfstudie gebruiken we `East US 2`. Zie de volgende [quickstart](../../../application-gateway/quick-create-cli.md) voor meer informatie.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

In de volgende stap gaat u een beschikbaarheidsset maken. Voer de volgende opdracht uit in Azure Cloud Shell en vervang `<resourceGroupName>` door de naam van de resourcegroep. Kies een naam voor `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Wanneer de opdracht is voltooid, zou u de volgende resultaten moeten hebben:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Virtuele machines met RHEL maken in de beschikbaarheidsset

> [!WARNING]
> Als u een PAYG-installatiekopie (Betalen per gebruik) kiest en hoge beschikbaarheid configureert, moet u uw abonnement mogelijk registreren. Dit kan tot gevolg hebben dat u twee keer betaalt voor het abonnement, omdat er kosten in rekening worden gebracht voor het Microsoft Azure RHEL-abonnement voor de virtuele machine en een abonnement op Red Hat. Voor meer informatie raadpleegt u https://access.redhat.com/solutions/2458541.
>
> Gebruik een RHEL-installatiekopie met hoge beschikbaarheid bij het maken van de Azure-VM om te voorkomen dat u dubbel wordt gefactureerd. Installatiekopieën die als RHEL-installatiekopieën met hoge beschikbaarheid worden aangeboden, zijn ook PAYG-installatiekopieën waarop opslagplaats met hoge beschikbaarheid vooraf is ingeschakeld.

1. Zo verkrijgt u een lijst met installatiekopieën van virtuele machines die RHEL met hoge beschikbaarheid aanbieden:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    U ziet de volgende resultaten:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    Voor deze zelfstudie kiezen we de installatiekopie `RedHat:RHEL-HA:7.6:7.6.2019062019` voor het RHEL 7-voorbeeld en kiest u `RedHat:RHEL-HA:8.1:8.1.2020021914` voor het RHEL 8-voorbeeld.
    
    U kunt ook SQL Server 2019 vooraf geïnstalleerd kiezen op installatiekopieën van RHEL8-HA. Voer de volgende opdracht uit om de lijst met installatiekopieën op te halen:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    U ziet de volgende resultaten:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Als u een van de bovenstaande installatiekopieën gebruikt om de virtuele machines te maken, is SQL Server 2019 vooraf geïnstalleerd. Sla de sectie [SQL Server en MSSQL-hulpprogramma's installeren](#install-sql-server-and-mssql-tools) over zoals beschreven in dit artikel.
    
    
    > [!IMPORTANT]
    > Namen van virtuele machines voor het instellen van een beschikbaarheidsgroep mogen maximaal vijftien tekens lang zijn. Een gebruikersnaam mag geen hoofdletters bevatten en wachtwoorden moeten langer zijn dan 12 tekens.

1. We willen drie VM's maken in de beschikbaarheidsset. Vervang het volgende in onderstaande opdracht:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`: bijvoorbeeld “Standard_D16_v3”
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Met de bovenstaande opdracht maakt u de virtuele machines en maakt u een standaard-VNet voor deze virtuele machines. Zie het artikel over [az vm create](/cli/azure/vm) voor meer informatie over de verschillende configuraties.

Wanneer de opdracht voor elke virtuele machine is voltooid, moet u ongeveer de volgende resultaten verkrijgen:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Met de standaardinstallatiekopie die met bovenstaande opdracht is gemaakt, wordt standaard een besturingssysteemschijf van 32 GB gemaakt. Met deze standaardinstallatie komt u mogelijk ruimte tekort. U kunt de volgende parameter aan bovenstaande `az vm create`-opdracht toevoegen om bijvoorbeeld een besturingssysteemschijf met 128 GB te maken: `--os-disk-size-gb 128`.
>
> Daarna kunt u [LVM (Logical Volume Manager) configureren](/previous-versions/azure/virtual-machines/linux/configure-lvm) als u de juiste mapvolumes moet uitbreiden om uw installatie te kunnen voltooien.

### <a name="test-connection-to-the-created-vms"></a>Verbinding met de gemaakte VM's testen

Maak verbinding met VM1 of de andere virtuele machines met behulp van de volgende opdracht in Azure Cloud Shell. Als u de IP's van uw VM's niet kunt vinden, volgt u deze [Quickstart over Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Als de verbinding is geslaagd, ziet u de volgende uitvoer die de Linux-terminal aangeeft:

```output
[<username>@<VM1> ~]$
```

Typ `exit` om de SSH-sessie te verlaten.

## <a name="enable-high-availability"></a>Hoge beschikbaarheid inschakelen

> [!IMPORTANT]
> Om dit gedeelte van de zelfstudie te kunnen voltooien, moet u een RHEL-abonnement hebben en de invoegtoepassing voor hoge beschikbaarheid. Als u een installatiekopie gebruikt die in de vorige sectie is aanbevolen, hoeft u geen ander abonnement te registreren.
 
Maak verbinding met elk VM-knooppunt en volg de onderstaande gids om hoge beschikbaarheid in te schakelen. Zie [Abonnement met hoge beschikbaarheid inschakelen voor RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel) voor meer informatie.

> [!TIP]
> Het is eenvoudiger als u op elk van de virtuele machines tegelijk een SSH-sessie opent, omdat dezelfde opdrachten in het hele artikel moeten worden uitgevoerd op elke VM.
>
> Als u meerdere `sudo`-opdrachten kopieert en plakt en u om een wachtwoord wordt gevraagd, worden de extra opdrachten niet uitgevoerd. Voer deze opdrachten afzonderlijk uit.


1. Voer de volgende opdrachten uit op elke virtuele machine om de Pacemaker-firewallpoorten te openen:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Update en installeer Pacemaker-pakketten op alle knooppunten met behulp van de volgende opdrachten:

    > [!NOTE]
    > **nmap** wordt als onderdeel van dit opdrachtenblok geïnstalleerd als een hulpprogramma voor het vinden van beschikbare IP-adressen in uw netwerk. U hoeft **nmap** niet te installeren, maar dit zal later in deze zelfstudie nuttig zijn.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Stel het wachtwoord in voor de standaardgebruiker die is gemaakt bij het installeren van Pacemaker-pakketten. Gebruik hetzelfde wachtwoord op alle knooppunten.

    ```bash
    sudo passwd hacluster
    ```

1. Gebruik de volgende opdracht om het hosts-bestand te openen en de omzetting van hostnamen in te stellen. Zie [Beschikbaarheidsgroep configureren](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) voor meer informatie over het configureren van het hosts-bestand.

    ```
    sudo vi /etc/hosts
    ```

    Voer in de **vi**-editor `i` in om tekst in te voegen en typ op een lege regel het **particuliere IP-adres** van de bijbehorende virtuele machine. Voeg vervolgens een spatie en de naam van de virtuele machine toe achter het IP-adres. Elke regel moet een afzonderlijke vermelding bevatten.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > U wordt aangeraden hierboven uw **particuliere IP**-adres te gebruiken. Als u in deze configuratie het openbare IP-adres gebruikt, zal de installatie mislukken; bovendien is het niet raadzaam om uw VM open te stellen voor externe netwerken.

    Als u de **vi**-editor wilt afsluiten, drukt u eerst op de **ESC**-toets en voert u vervolgens de opdracht `:wq` in om het bestand te schrijven en af te sluiten.

## <a name="create-the-pacemaker-cluster"></a>Het Pacemaker-cluster maken

In deze sectie gaat u de pcsd-service inschakelen en starten. Daarna gaat u het cluster configureren. Voor SQL Server on Linux worden de clusterresources niet automatisch gemaakt. U moet de Pacemaker-resources handmatig inschakelen en maken. Zie voor meer informatie het artikel over [het configureren van een failover-clusterexemplaar voor RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Pcsd-service en Pacemaker inschakelen en starten

1. Voer de opdrachten uit op alle knooppunten. Met deze opdrachten kunnen de knooppunten opnieuw worden samengevoegd met het cluster nadat het systeem opnieuw is opgestart.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Verwijder alle bestaande clusterconfiguraties uit alle knooppunten. Voer de volgende opdracht uit:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Voer op het primaire knooppunt de volgende opdrachten uit om het cluster in te stellen.

    - Wanneer u de opdracht `pcs cluster auth` uitvoert om de clusterknooppunten te verifiëren, wordt u gevraagd een wachtwoord op te geven. Voer het wachtwoord in voor de **hacluster**-gebruiker die u eerder hebt gemaakt.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    Voor RHEL 8 moet u de knooppunten afzonderlijk verifiëren. Voer de gebruikersnaam en het wachtwoord voor **hacluster** handmatig in wanneer u hierom wordt gevraagd.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Voer de volgende opdracht uit om te controleren of alle knooppunten online zijn.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Als alle knooppunten online zijn, ziet u ongeveer de volgende uitvoer:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Stel de verwachte stemmen in het live-cluster in op 3. Deze opdracht is alleen van invloed op het live-cluster; de configuratiebestanden veranderen niet.

    Stel op alle knooppunten de verwachte stemmen in met de volgende opdracht:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>De fencing-agent configureren

Een STONITH-apparaat biedt een fencing-agent. De onderstaande instructies zijn voor deze zelfstudie gewijzigd. Zie [Een STONITH-apparaat maken](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device) voor meer informatie.
 
[Controleer de versie van de Azure Fence-agent om te controleren of deze is bijgewerkt](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Gebruik de volgende opdracht:

```bash
sudo yum info fence-agents-azure-arm
```

De uitvoer moet ongeveer overeenkomen met onderstaand voorbeeld.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Een nieuwe toepassing registreren in Azure Active Directory
 
 1. Ga naar https://portal.azure.com
 2. Open de blade [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Ga naar Eigenschappen en noteer de map-id. Dit is de `tenant ID`
 3. Klik op [**App-registraties**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klik op **Nieuwe registratie**
 5. Voer een **Naam** in, bijvoorbeeld `<resourceGroupName>-app` en selecteer **Alleen accounts in deze organisatiemap**
 6. Selecteer het toepassingstype **Web**, voer een aanmeldings-URL in (bijvoorbeeld http://localhost) ) en klik op Toevoegen. De aanmeldings-URL wordt niet gebruikt en kan elke geldige URL zijn. Als u klaar bent, klikt u op **Registreren**
 7. Selecteer **Certificaten en geheimen** voor uw nieuwe app-registratie en klik vervolgens op **Nieuw clientgeheim**
 8. Voer een beschrijving in voor een nieuwe sleutel (clientgeheim), selecteer **Verloopt nooit** en klik op **Toevoegen**
 9. Noteer de waarde van het geheim. Dit wordt gebruikt als het wachtwoord van de service-principal
10. Selecteer **Overzicht**. Noteer de toepassings-id. Deze wordt gebruikt als de gebruikersnaam (aanmeldings-id in de onderstaande stappen) van de service-principal
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Een aangepaste rol maken voor de fencing-agent

Volg de zelfstudie [Een aangepaste Azure-rol maken met Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Het json-bestand moet er nu ongeveer zo uitzien:

- Vervang `<username>` door een naam van uw keuze. Dit is om te voorkomen dat u dubbele roldefinities maakt.
- Vervang `<subscriptionId>` door de id van uw Azure-abonnement.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Voer de volgende opdracht uit om de rol toe te voegen:

- Vervang `<filename>` door de naam van het bestand.
- Als u de opdracht uitvoert vanaf een ander pad dan de map waarin het bestand is opgeslagen, neemt u het mappad van het bestand op in de opdracht.

```bash
az role definition create --role-definition "<filename>.json"
```

In dat geval moet de volgende uitvoer worden weergegeven:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>De aangepaste rol toewijzen aan de service-principal

Wijs de aangepaste rol `Linux Fence Agent Role-<username>` die in de laatste stap is gemaakt, toe aan de service-principal. Gebruik de rol van Eigenaar niet meer!
 
1. Ga naar https://portal.azure.com
2. Open de blade [Alle resources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. De virtuele machine van het eerste clusterknooppunt selecteren
4. Klik op **Toegangsbeheer (IAM)**
5. Klik op **Een roltoewijzing toevoegen**
6. Selecteer de rol `Linux Fence Agent Role-<username>` in de lijst **Rol**
7. Voer in de lijst **Selecteren** de naam in van de toepassing die u hierboven hebt gemaakt, `<resourceGroupName>-app`
8. Klik op **Opslaan**.
9. Herhaal bovenstaande stappen voor alle clusterknooppunten.

### <a name="create-the-stonith-devices"></a>STONITH-apparaten maken

Voer de volgende opdrachten uit op knooppunt 1:

- Vervang de `<ApplicationID>` door de id uit uw toepassingsregistratie.
- Vervang het `<servicePrincipalPassword>` door de waarde uit het clientgeheim.
- Vervang de `<resourceGroupName>` door de resourcegroep uit uw abonnement dat u voor deze zelfstudie hebt gebruikt.
- Vervang de `<tenantID>` en de `<subscriptionId>` door die uit uw Azure-abonnement.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Omdat u al een regel aan de firewall hebt toegevoegd om de service voor hoge beschikbaarheid (`--add-service=high-availability`) toe te staan, hoeft u de volgende firewallpoorten niet meer op alle knooppunten te openen: 2224, 3121, 21064, 5405. Als u echter verbindingsproblemen met hoge beschikbaarheid ondervindt, gebruik dan de volgende opdracht om deze poorten te openen die zijn gekoppeld aan hoge beschikbaarheid.

> [!TIP]
> Om tijd te besparen, zou u ook alle poorten in deze zelfstudie tegelijk kunnen toevoegen. Welke poorten moeten worden geopend, wordt uitgelegd in de desbetreffende secties hieronder. Als u nu alle poorten wilt toevoegen, voegt u de aanvullende poorten toe: 1433 en 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server en MSSQL-hulpprogramma's installeren

> [!NOTE]
> Als u de VM's hebt gemaakt met de SQL Server 2019 vooraf geïnstalleerd op RHEL8, kunt u de onderstaande stappen overslaan om SQL Server en MSSQL-hulpprogramma's te installeren en de sectie **Een beschikbaarheidsgroep configureren** te starten nadat u het sa-wachtwoord op alle VM's hebt ingesteld door de opdracht `sudo /opt/mssql/bin/mssql-conf set-sa-password` op alle virtuele machines uit te voeren.

Gebruik de onderstaande sectie om SQL Server en MSSQL-hulpprogramma's op de virtuele machines te installeren. U kunt een van de onderstaande voorbeelden kiezen om SQL Server 2017 te installeren op RHEL 7 of SQL Server 2019 op RHEL 8. Voer elk van deze acties op alle knooppunten uit. Zie [SQL Server installeren op een virtuele Red Hat-machine](/sql/linux/quickstart-install-connect-red-hat) voor meer informatie.


### <a name="installing-sql-server-on-the-vms"></a>SQL Server installeren op de virtuele machines

De volgende opdrachten worden gebruikt om SQL Server te installeren:

**RHEL 7 met SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 met SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Open firewallpoort 1433 voor externe verbindingen

U moet poort 1433 op de VM openen om extern verbinding te kunnen maken. Gebruik de volgende opdrachten om poort 1433 te openen in de firewall van elke VM:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server-opdrachtregelprogramma's installeren

De volgende opdrachten worden gebruikt om SQL Server-opdrachtregelprogramma's te installeren. Zie [De SQL Server-opdrachtregelprogramma's installeren](/sql/linux/quickstart-install-connect-red-hat#tools) voor meer informatie.

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Voor het gemak voegt u /opt/mssql-tools/bin/ aan de omgevingsvariabele PATH toe. U kunt de hulpprogramma's dan uitvoeren zonder het volledige pad op te geven. Voer de volgende opdrachten uit als u PATH wilt wijzigen voor zowel aanmeldingssessies als interactieve/niet-aanmeldingssessies:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>De status van SQL Server controleren

Wanneer u klaar bent met de configuratie, kunt u de status van SQL Server controleren en verifiëren of deze wordt uitgevoerd:

```bash
systemctl status mssql-server --no-pager
```

In dat geval moet de volgende uitvoer worden weergegeven:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Een beschikbaarheidsgroep configureren

Gebruik de volgende stappen om een AlwaysOn-beschikbaarheidsgroep in SQL Server voor uw VM's te configureren. Zie [AlwaysOn-beschikbaarheidsgroep in SQL Server configureren voor hoge beschikbaarheid op Linux](/sql/linux/sql-server-linux-availability-group-configure-ha) voor meer informatie

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>AlwaysOn-beschikbaarheidsgroepen inschakelen en MSSQL-server opnieuw starten

Schakel AlwaysOn-beschikbaarheidsgroepen in op elk knooppunt dat als host fungeert voor een SQL Server-exemplaar. Start vervolgens de MSSQL-server opnieuw op. Voer het volgende script uit:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Een certificaat maken

AD-verificatie wordt momenteel niet ondersteund voor het eindpunt van de beschikbaarheidsgroep. Daarom moeten we een certificaat gebruiken voor eindpuntversleuteling voor beschikbaarheidsgroepen.

1. Maak verbinding met **alle knooppunten** met behulp van SQL Server Management Studio (SSMS) of SQL CMD. Voer de volgende opdrachten uit om een AlwaysOn_health-sessie in te schakelen en een hoofdsleutel te maken:

    > [!IMPORTANT]
    > Als u extern verbinding maakt met uw SQL Server-exemplaar, moet u poort 1433 op de firewall hebben geopend. U moet ook voor elke VM binnenkomende verbindingen met poort 1433 in uw NSG toestaan. Zie [Een beveiligingsregel maken](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) voor informatie over het maken van een beveiligingsregel voor binnenkomend verkeer voor meer informatie.

    - Vervang het `<Master_Key_Password>` door uw eigen wachtwoord.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Maak verbinding met de primaire replica met behulp van SSMS of SQL CMD. Met de onderstaande opdrachten maakt u een certificaat op `/var/opt/mssql/data/dbm_certificate.cer` en een persoonlijke sleutel op `var/opt/mssql/data/dbm_certificate.pvk` op uw primaire SQL Server-replica:

    - Vervang het `<Private_Key_Password>` door uw eigen wachtwoord.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Sluit de SQL CMD-sessie af door de opdracht `exit` uit te voeren, en ga terug naar uw SSH-sessie.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Het certificaat kopiëren naar de secundaire replica's en de certificaten maken op de server

1. Kopieer de twee bestanden die op dezelfde locatie zijn gemaakt naar alle servers die de beschikbaarheidsreplica's zullen hosten.
 
    Voer op de primaire server de volgende `scp`-opdracht uit om het certificaat naar de doelservers te kopiëren:

    - Vervang `<username>` en `<VM2>` door de gebruikersnaam en de doel-VM die u gebruikt.
    - Voer deze opdracht uit voor alle secundaire replica's.

    > [!NOTE]
    > U hoeft `sudo -i`, voor het retourneren van de hoofdomgeving, niet uit te voeren. U hoeft alleen de opdracht `sudo` vóór elke opdracht uit te voeren zoals eerder in deze zelfstudie.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Voer op de doelserver de volgende opdracht uit:

    - Vervang `<username>` door uw gebruikersnaam.
    - Met de opdracht `mv` verplaatst u de bestanden of map van de ene locatie naar een andere.
    - De opdracht `chown` wordt gebruikt om de eigenaar en groep met bestanden, mappen of koppelingen te wijzigen.
    - Voer deze opdrachten uit voor alle secundaire replica's.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Met het volgende Transact-SQL-script maakt u een certificaat vanuit de back-up die u op de primaire SQL Server-replica hebt gemaakt. Werk het script bij met sterke wachtwoorden. Het wachtwoord voor ontsleuteling is hetzelfde als het wachtwoord dat u hebt gebruikt voor het maken van het PVK-bestand in de vorige stap. Om het certificaat te maken, voert u op alle secundaire servers het volgende script uit met behulp van SQL CMD of SQL Server Management Studio:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>De eindpunten voor databasespiegeling maken op alle replica's

Voer op alle SQL Server-exemplaren het volgende script uit met behulp van SQL CMD of SQL Server Management Studio:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>De beschikbaarheidsgroep maken

Maak met behulp van SQL CMD of SQL Server Management Studio verbinding met het SQL Server-exemplaar dat als host fungeert voor de primaire replica. Voer de volgende opdracht uit om de beschikbaarheidsgroep te maken:

- Vervang `ag1` door de naam van de gewenste beschikbaarheidsgroep.
- Vervang de waarden van `<VM1>`, `<VM2>` en `<VM3>` door de namen van de SQL Server-exemplaren die de replica's hosten.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Een SQL Server-aanmelding maken voor Pacemaker

Maak op alle SQL Server-exemplaren een SQL Server-aanmelding voor Pacemaker. Met het volgende Transact-SQL-script maakt u een aanmelding.

- Vervang `<password>` door uw eigen complexe wachtwoord.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Sla op alle SQL Server-exemplaren de referenties op die voor de SQL Server aanmelding worden gebruikt. 

1. Zo maakt u het bestand:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Voeg de volgende twee regels toe aan het bestand:

    ```bash
    pacemakerLogin
    <password>
    ```

    Als u de **vi**-editor wilt afsluiten, drukt u eerst op de **ESC**-toets en voert u vervolgens de opdracht `:wq` in om het bestand te schrijven en af te sluiten.

1. Zo maakt u het bestand alleen leesbaar voor de hoofdmap:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Secundaire replica's toevoegen aan de beschikbaarheidsgroep

1. Als u de secundaire replica's wilt toevoegen aan de beschikbaarheidsgroep, moet u poort 5022 op de firewall openen voor alle servers. Voer de volgende opdracht uit in uw SSH-sessie:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Voer op de secundaire replica's de volgende opdrachten uit om ze toe te voegen aan de beschikbaarheidsgroep:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Voer het volgende Transact-SQL-script uit op de primaire replica en elke secundaire replica:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Wanneer de secundaire replica's zijn toegevoegd, kunt u ze bekijken in de Objectverkenner van SQL Server Management Studio door het knooppunt **AlwaysOn hoge beschikbaarheid** uit te vouwen:

    ![Schermopname van de primaire en secundaire beschikbaarheidsreplica's.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Een database toevoegen aan de beschikbaarheidsgroep

We volgen het artikel [Beschikbaarheidsgroep configureren](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group) over het toevoegen van een database.

In deze stap worden de volgende Transact-SQL-opdrachten gebruikt. Voer deze opdrachten uit in de primaire replica:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Controleren of de database is gemaakt op de secundaire servers

Voer op elke secundaire SQL Server-replica de volgende query uit om te zien of de db1-database is gemaakt en de status GESYNCHRONISEERD heeft:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Als de `synchronization_state_desc` GESYNCHRONISEERD vermeldt voor `db1`, betekent dit dat de replica's zijn gesynchroniseerd. De secundaire replica's bevatten `db1` in de primaire replica.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Resources voor beschikbaarheidsgroepen maken in het Pacemaker-cluster

We volgen de gids voor het [maken van resources voor beschikbaarheidsgroepen in het Pacemaker-cluster](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>De clusterresource voor de beschikbaarheidsgroep maken

1. Gebruik een van de volgende opdrachten op basis van de omgeving die u eerder hebt gekozen om de resource `ag_cluster` te maken in de beschikbaarheidsgroep `ag1`.

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Controleer de resource en verifieer of ze online zijn voordat u doorgaat met de volgende opdracht:

    ```bash
    sudo pcs resource
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Een resource maken voor een virtueel IP-adres

1. Gebruik een beschikbaar statisch IP-adres van uw netwerk om een resource te maken voor een virtueel IP-adres. U kunt er een vinden met behulp van de opdracht `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Stel de eigenschap **stonith-enabled** in op onwaar

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Maak de resource voor het virtuele IP-adres met behulp van de volgende opdracht:

    - Vervang de waarde van `<availableIP>` hieronder door een ongebruikt IP-adres.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. Om er zeker van te zijn dat het IP-adres en de resource van de beschikbaarheidsgroep op hetzelfde knooppunt worden uitgevoerd, moet u een co-locatiebeperking configureren. Voer de volgende opdracht uit:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Maak een volgordebeperking om ervoor te zorgen dat de resource van de beschikbaarheidsgroep actief is en wordt uitgevoerd vóór het IP-adres. Hoewel de co-locatiebeperking een volgordebeperking impliceert, wordt deze hiermee afgedwongen.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Voer de volgende opdracht uit om de beperkingen te controleren:

    ```bash
    sudo pcs constraint list --full
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Stonith opnieuw inschakelen

We zijn klaar om te testen. Schakel stonith in het cluster opnieuw in door de volgende opdracht uit te voeren op knooppunt 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>De clusterstatus controleren

U kunt de status van de clusterresources controleren met de volgende opdracht:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testfailover

We gaan een failover testen om er zeker van te zijn dat de configuratie tot dusver is geslaagd. Zie [Failover van AlwaysOn-beschikbaarheidsgroep op Linux](/sql/linux/sql-server-linux-availability-group-failover-ha) voor meer informatie.

1. Voer de volgende opdracht uit om handmatig een failover van de primaire replica uit te voeren naar `<VM2>`. Vervang `<VM2>` door de waarde van de servernaam.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   U kunt ook een extra optie opgeven, zodat de tijdelijke beperking die wordt gemaakt om de resource te verplaatsen naar een gewenst knooppunt, automatisch wordt uitgeschakeld. U hoeft de stappen 2 en 3 hieronder niet uit te voeren.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Een ander alternatief voor het automatiseren van stappen 2 en 3 hieronder waarmee de tijdelijke beperking voor de opdracht voor het verplaatsen van de resource wordt gewist, is door meerdere opdrachten op één regel te combineren. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Als u de beperkingen opnieuw controleert, ziet u dat er vanwege de handmatige failover een andere beperking is toegevoegd:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Verwijder de beperking met ID `cli-prefer-ag_cluster-master` met behulp van de volgende opdracht:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Als u uw clusterresources controleert met de opdracht `sudo pcs resource`, ziet u dat het primaire exemplaar nu `<VM2>` is.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Fencing testen

U kunt STONITH nu testen door de volgende opdracht uit te voeren. Probeer onderstaande opdracht vanuit `<VM1>` uit te voeren voor `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Met de fence-actie wordt het knooppunt standaard uit- en weer ingeschakeld. Als u het knooppunt alleen offline wilt zetten, gebruikt u de optie `--off` in de opdracht.

U zou de volgende uitvoer moeten zien:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Zie voor meer informatie over het testen van een fencing-apparaat het volgende [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)-artikel.

## <a name="next-steps"></a>Volgende stappen

Als u een beschikbaarheidsgroep-listener voor uw SQL Server-exemplaren wilt gebruiken, moet u een load balancer maken en configureren.

> [!div class="nextstepaction"]
> [Zelfstudie: Een listener voor beschikbaarheidsgroep configureren voor SQL Server in virtuele RHEL-machines in Azure](rhel-high-availability-listener-tutorial.md)
