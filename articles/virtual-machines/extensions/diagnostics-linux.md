---
title: Azure Compute - Diagnostische Linux-extensie 4.0
description: De Diagnostische extensie voor Azure Linux (LAD) 4.0 configureren voor het verzamelen van metrische gegevens en logboekgebeurtenissen van linux-VM's die worden uitgevoerd in Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 2e862915bcc524db50e7e66c969b713f729c64aa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479641"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>De diagnostische Linux-extensie 4.0 gebruiken om metrische gegevens en logboeken te bewaken

In dit document worden de nieuwste versies van de diagnostische Linux-extensie (LAD) beschreven.

> [!IMPORTANT]
> Zie De diagnostische  [Linux-extensie 3.0](./diagnostics-linux-v3.md)gebruiken om metrische gegevens en logboeken te bewaken voor meer informatie over versie 3.x. Zie Monitor the performance and diagnostic data of a Linux VM (De prestaties en diagnostische gegevens van een [Linux-VM](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)bewaken) voor meer informatie over versie 2.3 en eerder.

## <a name="introduction"></a>Introductie

met de diagnostische Linux-extensie kan een gebruiker de status bewaken van een Linux-VM die wordt uitgevoerd op Microsoft Azure. Het biedt de volgende mogelijkheden:

* Verzamelt metrische gegevens over systeemprestaties van de VM en slaat deze op in een specifieke tabel in een aangewezen opslagaccount.
* Hiermee worden logboekgebeurtenissen opgehaald uit syslog en opgeslagen in een specifieke tabel in het aangewezen opslagaccount.
* Hiermee kunnen gebruikers de metrische gegevens aanpassen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers de syslog-faciliteiten en de ernstniveaus aanpassen van gebeurtenissen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers opgegeven logboekbestanden uploaden naar een aangewezen opslagtabel.
* Ondersteunt het verzenden van metrische gegevens en logboekgebeurtenissen naar willekeurige EventHub-eindpunten en blobs in JSON-indeling in het aangewezen opslagaccount.

Deze extensie werkt met beide Azure-implementatiemodellen.

## <a name="install-the-extension-on-a-vm"></a>De extensie installeren op een VM

U kunt deze extensie inschakelen met behulp van de Azure PowerShell-cmdlets, Azure CLI-scripts, Azure Resource Manager-sjablonen (ARM-sjablonen) of de Azure Portal. Zie Extensies en functies [voor meer informatie.](features-linux.md)

>[!NOTE]
>Sommige onderdelen van de Linux Diagnostic VM-extensie worden ook verzonden in de [Log Analytics VM-extensie](./oms-linux.md). Vanwege deze architectuur kunnen er conflicten optreden als beide extensies in dezelfde ARM-sjabloon worden gemaakt. 
>
>Om conflicten tijdens de installatie te voorkomen, gebruikt u de [ `dependsOn` -richtlijn om](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) de extensies opeenvolgend te installeren. De extensies kunnen in beide volgorde worden geïnstalleerd.

Gebruik de installatie-instructies en een [downloadbare voorbeeldconfiguratie om](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 4.0 te configureren voor:

* Leg dezelfde metrische gegevens vast en sla deze op die in LAD-versies 2.3 en 3.x zijn opgegeven.
* Verzend metrische gegevens naar Azure Monitor sink, samen met de gebruikelijke sink naar Azure Storage. Deze functionaliteit is nieuw in LAD 4.0.
* Leg een handige set metrische gegevens van het bestandssysteem vast, zoals in LAD 3.0.
* Leg de standaardverzameling Syslog vast die is ingeschakeld door LAD 2.3.
* Schakel de Azure Portal in voor grafieken en waarschuwingen voor metrische gegevens van VM's.

De downloadbare configuratie is slechts een voorbeeld. Pas deze aan uw behoeften aan.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

De diagnostische Linux-extensie ondersteunt veel distributies en versies. De volgende lijst met distributies en versies is alleen van toepassing op door Azure goedgekeurde Linux-leveranciers. De extensie biedt over het algemeen geen ondersteuning voor BYOL- en BYOS-afbeeldingen van derden, zoals apparaten.

Een distributie met alleen de belangrijkste versies, zoals Debian 7, wordt ook ondersteund voor alle secundaire versies. Als er een specifieke secundaire versie is opgegeven, wordt alleen die versie ondersteund. Als er een plusteken (+) wordt toegevoegd, worden secundaire versies ondersteund die gelijk zijn aan of hoger zijn dan de opgegeven versie.

Ondersteunde distributies en versies:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Vereisten

* **Azure Linux-agent versie 2.2.0 of hoger.** De meeste Azure VM Linux-galerie-afbeeldingen bevatten versie 2.2.7 of hoger. Voer `/usr/sbin/waagent -version` uit om te bevestigen welke versie op de VM is geïnstalleerd. Als op de VM een oudere versie van de gastagent wordt uitgevoerd, werkt [u de Linux-agent bij.](./update-linux-agent.md)
* **Azure CLI**. [Stel de Azure CLI-omgeving](/cli/azure/install-azure-cli) in op uw computer.
* **De `wget` opdracht**. Als u deze nog niet hebt, voer dan `sudo apt-get install wget` uit.
* **Een Azure-abonnement en een opslagaccount voor algemeen gebruik om** de gegevens op te slaan.  Opslagaccounts voor algemeen gebruik ondersteunen table storage, wat vereist is.  Een Blob Storage-account werkt niet.
* **Python 2.**

### <a name="python-requirement"></a>Python-vereiste

Voor de diagnostische Linux-extensie is Python 2 vereist. Als uw virtuele machine gebruikmaakt van een distributie die standaard geen Python 2 bevat, installeert u deze. 

Met de volgende voorbeeldopdrachten wordt Python 2 geïnstalleerd op verschillende distributies:    

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

Het `python2` uitvoerbare bestand moet een alias hebben voor *python*. Hier is één manier om deze alias in te stellen:

1. Voer de volgende opdracht uit om bestaande aliassen te verwijderen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Voer de volgende opdracht uit om de alias te maken.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Voorbeeldinstallatie

> [!NOTE]
> Vul voor de volgende voorbeelden de juiste waarden in voor de variabelen in de eerste sectie voordat u de code gaat uitvoeren. 

In deze voorbeelden verzamelt de voorbeeldconfiguratie een set standaardgegevens en verzendt deze naar table storage. De URL voor de voorbeeldconfiguratie en de inhoud ervan kunnen worden gewijzigd. 

In de meeste gevallen moet u een kopie van het JSON-bestand met portalinstellingen downloaden en aanpassen aan uw behoeften. Gebruik vervolgens sjablonen of uw eigen automatisering om een aangepaste versie van het configuratiebestand te gebruiken in plaats van elke keer van de URL te downloaden.

> [!NOTE]
> Wanneer u de nieuwe Azure Monitor-sink inschakelen, moet voor de VM's een door het systeem toegewezen identiteit zijn ingeschakeld voor het genereren van MSI-verificatietokens (Managed Service Identity). U kunt deze instellingen toevoegen tijdens of na het maken van de VM. 
>
> Zie Beheerde Azure Portal configureren voor instructies voor de Azure Portal, de Azure CLI, PowerShell en [Azure Resource Manager.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) 



#### <a name="azure-cli-sample"></a>Azure CLI-voorbeeld

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-on-a-virtual-machine-scale-set-instance"></a>Azure CLI-voorbeeld voor het installeren van LAD 4.0 op een exemplaar van een virtuele-machineschaalset

```azurecli
# Set your Azure virtual machine scale set diagnostic variables. 
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="update-the-extension-settings"></a>De extensie-instellingen bijwerken

Nadat u de beveiligde of openbare instellingen hebt gewijzigd, implementeert u deze op de VM door dezelfde opdracht uit te voeren. Als er instellingen zijn gewijzigd, worden de updates verzonden naar de extensie. LAD laadt de configuratie opnieuw en start zichzelf opnieuw op.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migreren vanuit eerdere versies van de extensie

De nieuwste versie van de extensie is *4.0, die momenteel in openbare preview is.* Oudere versies van 3.x worden nog steeds ondersteund. Maar 2.x-versies zijn afgeschaft sinds 31 juli 2018.

> [!IMPORTANT]
> Als u wilt migreren van 3.x naar de nieuwste versie van de extensie, verwijdert u de oude extensie. Installeer vervolgens versie 4, die de bijgewerkte configuratie bevat voor de door het systeem toegewezen identiteit en sinks voor het verzenden van metrische gegevens naar Azure Monitor sink.

Wanneer u de nieuwe extensie installeert, moet u automatische upgrades van secundaire versies inschakelen:
* Geef op klassieke implementatiemodel-VM's versie op als u de extensie `4.*` installeert via de Azure Xplat CLI of PowerShell.
* Neem Azure Resource Manager VM's voor het implementatiemodel `"autoUpgradeMinorVersion": true` op in de VM-implementatiesjabloon.

U kunt hetzelfde opslagaccount gebruiken als voor LAD 3.x. 

## <a name="protected-settings"></a>Beveiligde instellingen

Deze set configuratiegegevens bevat gevoelige informatie die moet worden beveiligd tegen openbare weergave. Het bevat bijvoorbeeld opslagreferenties. De instellingen worden versleuteld verzonden naar en opgeslagen door de extensie.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Waarde
---- | -----
storageAccountName | De naam van het opslagaccount waarin de extensie gegevens schrijft.
storageAccountEndPoint | (Optioneel) Het eindpunt dat de cloud identificeert waarin het opslagaccount bestaat. Als deze instelling niet aanwezig is, maakt LAD standaard gebruik van de openbare Cloud van Azure, `https://core.windows.net` . Als u een opslagaccount wilt gebruiken in Azure Duitsland, Azure Government of Azure China 21Vianet, stelt u deze waarde zo nodig in.
storageAccountSasToken | Een [SAS-token voor het account](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) voor blob- en tabelservices ( `ss='bt'` ). Dit token is van toepassing op containers en objecten ( `srt='co'` ). Er worden machtigingen voor toevoegen, maken, lijst, bijwerken en schrijven verleend ( `sp='acluw'` ). Neem *niet* het vooraanstaand vraagteken (?) op.
mdsdHttpProxy | (Optioneel) HTTP-proxygegevens die de extensie nodig heeft om verbinding te maken met het opgegeven opslagaccount en eindpunt.
sinksConfig | (Optioneel) Details van alternatieve bestemmingen waaraan metrische gegevens en gebeurtenissen kunnen worden geleverd. De volgende secties bevatten details over elke gegevenss sink die door de extensie wordt ondersteund.

Gebruik de functie om een SAS-token op te halen in een `listAccountSas` ARM-sjabloon. Zie List function example voor een [voorbeeldsjabloon.](../../azure-resource-manager/templates/template-functions-resource.md#list-example)

U kunt het vereiste SAS-token maken via de Azure Portal:

1. Selecteer het opslagaccount voor algemeen gebruik waarvoor u de extensie wilt schrijven.
1. Selecteer in het menu aan de linkerkant onder **Instellingen** de optie **Shared Access Signature.**
1. Maak de selecties zoals eerder beschreven.
1. Selecteer **SAS genereren.**

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Schermopname van de pagina Shared Access Signature met de knop Generate S A S.":::

Kopieer de gegenereerde SAS naar het `storageAccountSasToken` veld . Verwijder het vooraanstaand vraagteken (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

De `sinksConfig` optionele sectie definieert meer bestemmingen waar de extensie verzamelde informatie naar verzendt. De `"sink"` matrix bevat een -object voor elke extra gegevenss sink. Het `"type"` kenmerk bepaalt de andere kenmerken in het -object.

Element | Waarde
------- | -----
naam | Een tekenreeks die wordt gebruikt om elders in de extensieconfiguratie naar deze sink te verwijzen.
type | Het type sink dat wordt gedefinieerd. Bepaalt de andere waarden (indien van) in exemplaren van dit type.

De diagnostische Linux-extensie 4.0 ondersteunt twee sinktypen: `EventHub` en `JsonBlob` .

#### <a name="eventhub-sink"></a>EventHub-sink

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

De `"sasURL"` vermelding bevat de volledige URL, inclusief het SAS-token, voor de Event Hub waarop gegevens moeten worden gepubliceerd. LAD vereist een SAS om een beleid te noemen waarmee de verzendclaim wordt mogelijk. Hier volgt een voorbeeld:

* Maak een Event Hubs naamruimte met de naam `contosohub` .
* Maak een Event Hub in de naamruimte met de naam `syslogmsgs` .
* Maak een beleid voor gedeelde toegang op de Event Hub met de `writer` naam waarmee de verzendclaim wordt gemaakt.

Als u een SAS hebt gemaakt die goed is tot middernacht UTC op 1 januari 2018, kan de waarde er ongeveer als `sasURL` volgt uit zien.

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Zie Een [SAS-token](/rest/api/eventhub/generate-sas-token#powershell)genereren voor meer informatie over het genereren en ophalen van informatie over SAS-tokens voor Event Hubs.

#### <a name="jsonblob-sink"></a>JsonBlob-sink

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Gegevens die naar een `JsonBlob` sink worden geleid, worden opgeslagen in blobs in Azure Storage. Elk exemplaar van LAD maakt elk uur een blob voor elke sinknaam. Elke blob bevat altijd een syntactisch geldige JSON-matrix met objecten. Nieuwe vermeldingen worden atomisch toegevoegd aan de matrix. 

Blobs worden opgeslagen in een container met dezelfde naam als de sink. De Azure Storage voor namen van blobcontainers zijn van toepassing op de namen van `JsonBlob` sinks. Dat wil zeggen dat namen tussen 3 en 63 alfanumerieke ASCII-tekens of streepjes in kleine letters moeten bevatten.

## <a name="public-settings"></a>Openbare instellingen

De structuur van de openbare instellingen bevat verschillende instellingenblokken die de informatie bepalen die door de extensie wordt verzameld. Elke instelling, behalve `ladCfg` , is optioneel. Als u metrische gegevens of syslog-verzameling opgeeft in `ladCfg` , moet u ook `StorageAccount` opgeven. U moet het element opgeven om de sink Azure Monitor voor metrische gegevens van `sinksConfig` LAD 4.0 in te stellen.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Element | Waarde
------- | -----
StorageAccount | De naam van het opslagaccount waarin de extensie gegevens schrijft. Moet de naam zijn die is opgegeven in de [beveiligde instellingen.](#protected-settings)
mdsdHttpProxy | (Optioneel) De proxy die is opgegeven in de [beveiligde instellingen](#protected-settings). Als de persoonlijke waarde is ingesteld, wordt de openbare waarde overgenomen. Plaats proxyinstellingen die een geheim bevatten, zoals een wachtwoord, in de [beveiligde instellingen.](#protected-settings)

De volgende secties bevatten details over de resterende elementen.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

De structuur bepaalt het verzamelen van metrische gegevens en logboeken voor levering aan de `ladCfg` Azure Monitor Metrics-service en andere gegevenss sinks. Geef een `performanceCounters` of `syslogEvents` beide op. Geef ook de structuur `metrics` op.

Als u syslog of het verzamelen van metrische gegevens niet wilt inschakelen, geeft u een lege structuur op voor het `ladCfg` element, zoals in dit voorbeeld: 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Element | Waarde
------- | -----
eventVolume | (Optioneel) Hiermee bepaalt u het aantal partities dat in de opslagtabel is gemaakt. De waarde moet `"Large"` , `"Medium"` of `"Small"` zijn. Als de waarde niet is opgegeven, is de standaardwaarde `"Medium"` .
sampleRateInSeconds | (Optioneel) Het standaardinterval tussen de verzameling onbewerkte (niet-geaggregeerde) metrische gegevens. De kleinste ondersteunde steekproeffrequentie is 15 seconden. Als de waarde niet is opgegeven, is de standaardwaarde `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Waarde
------- | -----
resourceId | De Azure Resource Manager resource-id van de virtuele machine of van de virtuele-machineschaalset waarvan de virtuele machine deel uitmaken. Geef deze instelling ook op als de configuratie gebruikmaakt van een `JsonBlob` sink.
scheduledTransferPeriod | De frequentie waarmee geaggregeerde metrische gegevens worden berekend en overgedragen naar Azure Monitor Metrics. De frequentie wordt uitgedrukt als een IS 8601-tijdsinterval. De kleinste overdrachtsperiode is 60 seconden, dat wil zeggen PT1M. Geef ten minste één `scheduledTransferPeriod` op.

Voorbeelden van de metrische gegevens die in de sectie zijn opgegeven, worden elke 15 seconden verzameld of op basis van de samplefrequentie die expliciet is gedefinieerd `performanceCounters` voor de teller. Als er `scheduledTransferPeriod` meerdere frequenties worden weergegeven, zoals in het voorbeeld, wordt elke aggregatie onafhankelijk berekend.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

De `performanceCounters` optionele sectie bepaalt de verzameling metrische gegevens. Onbewerkte voorbeelden worden geaggregeerd voor [`scheduledTransferPeriod`](#metrics) elke om deze waarden te produceren:

* Gemiddeld
* Minimum
* Maximum
* Laatst verzamelde waarde
* Aantal onbewerkte voorbeelden dat wordt gebruikt om de aggregatie te berekenen

Element | Waarde
------- | -----
Putten | (Optioneel) Een door komma's gescheiden lijst met namen van sinks waar LAD geaggregeerde metrische resultaten naar verzendt. Alle geaggregeerde metrische gegevens worden gepubliceerd naar elke vermelde sink. Bijvoorbeeld: `"EHsink1, myjsonsink"`. Zie [`sinksConfig`](#sinksconfig) voor meer informatie. 
type | Identificeert de werkelijke provider van de metrische waarde.
klasse | Identificeert `"counter"` samen met de specifieke metrische gegevens binnen de naamruimte van de provider.
counter | Identificeert `"class"` samen met de specifieke metrische gegevens binnen de naamruimte van de provider.
counterSpecifier | Identificeert de specifieke metrische gegevens in de Azure Monitor naamruimte Metrics.
Voorwaarde | (Optioneel) Hiermee selecteert u een exemplaar van het object waarop de metrische gegevens van toepassing zijn. Of selecteert de aggregatie voor alle exemplaren van dat object. 
Samplerate | Het IS 8601-interval waarmee de snelheid wordt bepaalt waarmee onbewerkte steekproeven voor deze metrische gegevens worden verzameld. Als de waarde niet is ingesteld, wordt het verzamelingsinterval ingesteld door de waarde van [`sampleRateInSeconds`](#ladcfg) . De kortste ondersteunde steekproeffrequentie is 15 seconden (PT15S).
eenheid | Hiermee definieert u de eenheid voor de metrische gegevens. Moet een van deze tekenreeksen `"Count"` zijn: `"Bytes"` , , , , , , `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Consumenten van de verzamelde gegevens verwachten dat de verzamelde gegevenswaarden overeenkomen met deze eenheid. LAD negeert dit veld.
displayName | Het label dat moet worden gekoppeld aan de gegevens in Azure Monitor Metrics. Dit label is in de taal die is opgegeven door de bijbehorende instelling voor de taal. LAD negeert dit veld.

De `counterSpecifier` is een willekeurige id. Gebruikers van metrische gegevens, zoals de functie Azure Portal grafieken en waarschuwingen, gebruiken als de 'sleutel' waarmee een metrische gegevens of een exemplaar van een metrische `counterSpecifier` gegevens worden geïdentificeerd. 

Voor `builtin` metrische gegevens raden we `counterSpecifier` waarden aan die beginnen met `/builtin/` . Als u een specifiek exemplaar van een metrische waarde verzamelt, koppelt u de id van het exemplaar aan de `counterSpecifier` waarde. Hier volgen enkele voorbeelden:

* `/builtin/Processor/PercentIdleTime` - Gemiddelde niet-actieve tijd voor alle vCCPUs
* `/builtin/Disk/FreeSpace(/mnt)` - Vrije ruimte voor het `/mnt` bestandssysteem
* `/builtin/Disk/FreeSpace` - Gemiddelde vrije ruimte voor alle aaneengestelde bestandssystemen

LAD en de Azure Portal verwacht niet dat de `counterSpecifier` waarde overeen komt met een patroon. Wees consistent in de manier waarop u waarden `counterSpecifier` maakt.

Wanneer u `performanceCounters` opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure Storage. Dezelfde gegevens kunnen worden geschreven naar JSON-blobs of Event Hubs of beide. U kunt het opslaan van gegevens in een tabel echter niet uitschakelen. 

Alle exemplaren van LAD die dezelfde opslagaccountnaam en hetzelfde eindpunt gebruiken, voegen hun metrische gegevens en logboeken toe aan dezelfde tabel. Als er te veel VM's naar dezelfde tabelpartitie schrijven, kan Azure schrijf naar die partitie beperkt. 

De `eventVolume` instelling zorgt ervoor dat vermeldingen worden verdeeld over 1 (klein), 10 (gemiddeld) of 100 (grote) partities. Normaal gesproken zijn middelgrote partities voldoende om verkeersbeperking te voorkomen. 

De functie Azure Monitor metrics van de Azure Portal gebruikt de gegevens in deze tabel om grafieken te produceren of om waarschuwingen te activeren. De tabelnaam is de samenvoeging van deze tekenreeksen:

* `WADMetrics`
* De `"scheduledTransferPeriod"` voor de geaggregeerde waarden die zijn opgeslagen in de tabel
* `P10DV2S`
* Een datum in de vorm 'YYYYMMDD', die elke 10 dagen wordt gewijzigd

Voorbeelden zijn `WADMetricsPT1HP10DV2S20170410` en `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

De `syslogEvents` optionele sectie bepaalt het verzamelen van logboekgebeurtenissen uit syslog. Als de sectie wordt weggelaten, worden syslog-gebeurtenissen helemaal niet vastgelegd.

De `syslogEventConfiguration` verzameling heeft één vermelding voor elke syslog-faciliteit die van belang is. Als voor een bepaalde faciliteit is of als die faciliteit helemaal niet in het element wordt weergegeven, worden er geen gebeurtenissen van `minSeverity` `"NONE"` die faciliteit vastgelegd.

Element | Waarde
------- | -----
Putten | Een door komma's gescheiden lijst met namen van sinks waarop afzonderlijke logboekgebeurtenissen worden gepubliceerd. Alle logboekgebeurtenissen die overeenkomen met de beperkingen in `syslogEventConfiguration` worden gepubliceerd naar elke vermelde sink. Voorbeeld: `"EHforsyslog"`
facilityName | De naam van een syslog-faciliteit, zoals `"LOG\_USER"` of `"LOG\_LOCAL0"` . Zie de sectie 'faciliteit' van de [syslog-manpagina voor meer informatie.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minSeverity | Een ernstniveau voor syslog, zoals `"LOG\_ERR"` of `"LOG\_INFO"` . Zie de sectie Niveau van de [pagina syslog man voor meer informatie.](http://man7.org/linux/man-pages/man3/syslog.3.html) De extensie legt gebeurtenissen vast die op of boven het opgegeven niveau naar de faciliteit zijn verzonden.

Wanneer u `syslogEvents` opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure Storage. Dezelfde gegevens kunnen worden geschreven naar JSON-blobs of Event Hubs of beide. U kunt het opslaan van gegevens in een tabel echter niet uitschakelen. 

Het partitioneringsgedrag voor deze tabel is hetzelfde als beschreven voor `performanceCounters` . De tabelnaam is de samenvoeging van deze tekenreeksen:

* `LinuxSyslog`
* Een datum in de vorm 'YYYYMMDD', die elke 10 dagen wordt gewijzigd

Voorbeelden zijn `LinuxSyslog20170410` en `LinuxSyslog20170609` .

### <a name="sinksconfig"></a>sinksConfig

Met de optionele sectie kunt u naast het opslagaccount en de standaardblade Metrische gegevens voor gasten ook Azure Monitor verzenden naar de `sinksConfig` sink.

> [!NOTE]
> Voor de sectie moet een door het systeem toegewezen identiteit zijn ingeschakeld op de VM's `sinksConfig` of virtuele-machineschaalset. U kunt een door het systeem toegewezen identiteit inschakelen via Azure Portal, CLI, PowerShell of Azure Resource Manager. Volg de [gedetailleerde instructies of](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) zie de eerdere installatiesvoorbeelden in dit artikel. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>fileLogs

De `fileLogs` sectie bepaalt het vastleggen van logboekbestanden. LAD legt nieuwe tekstregels vast wanneer deze naar het bestand worden geschreven. Deze worden naar tabelrijen en/of opgegeven sinks, zoals en , `JsonBlob` `EventHub` wegschreven.

> [!NOTE]
> De `fileLogs` worden vastgelegd door een subcomponent van LAD met de naam `omsagent` . Als u `fileLogs` wilt verzamelen, moet u `omsagent` ervoor zorgen dat de gebruiker leesmachtigingen heeft voor de bestanden die u opgeeft. Het moet ook uitvoermachtigingen hebben voor alle directories in het pad naar dat bestand. Nadat LAD is geïnstalleerd, kunt u machtigingen controleren door uit te `sudo su omsagent -c 'cat /path/to/file'` gaan.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Waarde
------- | -----
file | De volledige padnaam van het logboekbestand dat moet worden gevolgd en vastgelegd. De padnaam is voor één bestand. Het mag geen map een naam geven of jokertekens bevatten. Het `omsagent` gebruikersaccount moet leestoegang hebben tot het bestandspad.
tabel | (Optioneel) De Azure Storage waarin nieuwe regels van de 'tail' van het bestand worden geschreven. De tabel moet zich in het aangewezen opslagaccount, zoals opgegeven in de beveiligde configuratie. 
Putten | (Optioneel) Een door komma's gescheiden lijst met namen van meer sinks waar logboekregels naar worden verzonden.

Of `"table"` `"sinks"` beide moeten worden opgegeven.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metrische gegevens die worden ondersteund door de ingebouwde provider

> [!NOTE]
> De standaardgegevens die LAD ondersteunt, worden geaggregeerd voor alle bestandssystemen, schijven of namen. Raadpleeg voor niet-geaggregeerde metrische gegevens de nieuwere Azure Monitor voor metrische sinkgegevens.

De `builtin` provider van metrische gegevens is een bron van metrische gegevens die het interessantst zijn voor een grote groep gebruikers. Deze metrische gegevens kunnen in vijf algemene klassen worden gebruikt:

* Processor
* Geheugen
* Netwerk
* Bestandssysteem
* Schijf

### <a name="builtin-metrics-for-the-processor-class"></a>ingebouwde metrische gegevens voor de processorklasse

De processorklasse van metrische gegevens biedt informatie over het processorgebruik in de VM. Wanneer percentages worden geaggregeerd, is het resultaat het gemiddelde voor alle CPU's. 

Als in een VM met twee vCPU's één vCPU voor 100 procent bezet is en de andere voor 100 procent inactief is, is de gerapporteerde `PercentIdleTime` VCPU 50. Als elke vCPU voor dezelfde periode 50 procent bezet is, is het gerapporteerde resultaat ook 50. Wanneer in een VM met vier vCPU's één vCPU voor 100 procent bezet is en de andere VCPU's inactief zijn, is de `PercentIdleTime` gerapporteerde VCPU 75.

Prestatiemeteritem | Betekenis
------- | -------
PercentIdleTime | Percentage tijd tijdens het aggregatievenster dat processors de inactieve kernellus hebben laten uitvoeren
PercentProcessorTime | Percentage tijd dat een niet-inactieve thread wordt uitgevoerd
PercentIOWaitTime | Percentage tijd dat moet worden gewacht tot I/O-bewerkingen zijn uitgevoerd
PercentInterruptTime | Percentage tijd dat hardware- of software-interrupts en DPC's worden uitgevoerd (uitgestelde procedure-aanroepen)
PercentUserTime | Van niet-niet-actieve tijd tijdens het aggregatievenster, het percentage tijd dat in de gebruikersmodus met de normale prioriteit is besteed
PercentNiceTime | Van niet-niet-actieve tijd heeft het percentage besteed met een lagere prioriteit (nice)
PercentPrivilegedTime | Van niet-niet-actieve tijd, het percentage dat is besteed in de modus bevoegde (kernel)

De eerste vier tellers moeten 100 procent bedragen. De laatste drie tellers worden ook opgeteld bij 100 procent. Deze drie tellers worden onderverdeeld in de som `PercentProcessorTime` van `PercentIOWaitTime` , en `PercentInterruptTime` .

### <a name="builtin-metrics-for-the-memory-class"></a>builtin metrics for the Memory class

De geheugenklasse van metrische gegevens biedt informatie over geheugengebruik, paginering en wisselen.

counter | Betekenis
------- | -------
AvailableMemory | Beschikbaar fysiek geheugen in MiB
PercentAvailableMemory | Beschikbaar fysiek geheugen als percentage van het totale geheugen
UsedMemory | In gebruik fysiek geheugen (MiB)
PercentUsedMemory | Fysiek geheugen in gebruik als percentage van het totale geheugen
PagesPerSec | Totaal aantal paginering (lezen/schrijven)
PagesReadPerSec | Pagina's die worden gelezen uit het backing-archief, zoals wisselbestand, programmabestand en mapped bestand
PagesWrittenPerSec | Pagina's die naar de back-store worden geschreven, zoals wisselbestand en mapped bestand
AvailableSwap | Ongebruikte wisselruimte (MiB)
PercentAvailableSwap | Ongebruikte wisselruimte als een percentage van de totale wissel
UsedSwap | In-use wisselruimte (MiB)
PercentUsedSwap | In-use wisselruimte als een percentage van de totale wissel

Deze klasse met metrische gegevens heeft slechts één exemplaar. Het `"condition"` kenmerk heeft geen nuttige instellingen en moet worden weggelaten.

### <a name="builtin-metrics-for-the-network-class"></a>builtin metrics for the Network class

De netwerkklasse van metrische gegevens biedt informatie over netwerkactiviteit op een afzonderlijke netwerkinterface sinds het opstarten. 

LAD maakt geen metrische bandbreedtegegevens beschikbaar. U kunt deze metrische gegevens op halen uit metrische gegevens van de host.

Prestatiemeteritem | Betekenis
------- | -------
BytesTransmitted | Totaal aantal verzonden bytes sinds het opstarten
BytesReceived | Totaal aantal bytes ontvangen sinds het opstarten
BytesTotal | Totaal aantal verzonden of ontvangen bytes sinds het opstarten
PakkettenTransmitted | Totaal aantal verzonden pakketten sinds het opstarten
PakkettenReceived | Totaal aantal pakketten ontvangen sinds het opstarten
TotalRxErrors | Aantal ontvangstfouten sinds het opstarten
TotalTxErrors | Aantal verzendfouten sinds het opstarten
TotalCollisions | Aantal door de netwerkpoorten gerapporteerde aanrijdingen sinds het opstarten

### <a name="builtin-metrics-for-the-file-system-class"></a>ingebouwde metrische gegevens voor de bestandssysteemklasse

De bestandssysteemklasse van metrische gegevens biedt informatie over het gebruik van bestandssysteem. Absolute waarden en percentagewaarden worden gerapporteerd zoals ze zouden worden weergegeven voor een gewone gebruiker (niet root).

Prestatiemeteritem | Betekenis
------- | -------
FreeSpace | Beschikbare schijfruimte in bytes
UsedSpace | Gebruikte schijfruimte in bytes
PercentFreeSpace | Percentage vrije ruimte
PercentUsedSpace | Percentage gebruikte ruimte
PercentFreeInodes | Percentage ongebruikte indexknooppunten (inodes)
PercentUsedInodes | Percentage toegewezen (in gebruik) inodes dat is opgeteld in alle bestandssystemen
BytesReadPerSecond | Gelezen bytes per seconde
BytesWrittenPerSecond | Geschreven bytes per seconde
BytesPerSecond | Gelezen of geschreven bytes per seconde
ReadsPerSecond | Leesbewerkingen per seconde
WritesPerSecond | Schrijfbewerkingen per seconde
TransfersPerSecond | Lees- of schrijfbewerkingen per seconde

### <a name="builtin-metrics-for-the-disk-class"></a>builtin metrics for the Disk class

De schijfklasse van metrische gegevens bevat informatie over het gebruik van schijfapparaat. Deze statistieken zijn van toepassing op het hele station. 

Wanneer een apparaat meerdere bestandssystemen heeft, worden de tellers voor dat apparaat effectief geaggregeerd in alle bestandssystemen.

Prestatiemeteritem | Betekenis
------- | -------
ReadsPerSecond | Leesbewerkingen per seconde
WritesPerSecond | Schrijfbewerkingen per seconde
TransfersPerSecond | Totaal aantal bewerkingen per seconde
AverageReadTime | Gemiddelde seconden per leesbewerking
AverageWriteTime | Gemiddelde seconden per schrijfbewerking
AverageTransferTime | Gemiddelde seconden per bewerking
AverageDiskQueueLength | Gemiddeld aantal schijfbewerkingen in de wachtrij
ReadBytesPerSecond | Aantal bytes gelezen per seconde
WriteBytesPerSecond | Aantal geschreven bytes per seconde
BytesPerSecond | Aantal gelezen of geschreven bytes per seconde

## <a name="install-and-configure-lad-40"></a>LAD 4.0 installeren en configureren

U kunt LAD 4.0 installeren en configureren in de Azure CLI of in PowerShell.

### <a name="azure-cli"></a>Azure CLI

Als uw beveiligde instellingen zich in het bestandsbestand *ProtectedSettings.js* op en uw openbare configuratiegegevens zich in dePublicSettings.js *op*, voer dan deze opdracht uit:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bij de opdracht wordt ervan uitgenomen dat u de Azure Resource Management-modus van de Azure CLI gebruikt. Als u LAD wilt configureren voor klassieke implementatiemodel-VM's, schakelt u over naar de asm-modus ( ) en laat u de naam van de `azure config mode asm` resourcegroep weg in de opdracht . 

Zie de platformoverschrijdende [CLI-documentatie voor meer informatie.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Als uw beveiligde instellingen zich in de variabele en uw openbare configuratiegegevens in de variabele `$protectedSettings` `$publicSettings` staan, moet u deze opdracht uitvoeren:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="example-lad-40-configuration"></a>Voorbeeld van LAD 4.0-configuratie

Op basis van de voorgaande definities biedt deze sectie een voorbeeld van een LAD 4.0-extensieconfiguratie en een uitleg. Als u dit voorbeeld wilt toepassen op uw case, gebruikt u de naam van uw eigen opslagaccount, SAS-token voor account en Event Hubs SAS-tokens.

> [!NOTE]
> Afhankelijk van of u de Azure CLI of PowerShell gebruikt om LAD te installeren, verschilt de methode voor het bieden van openbare en beveiligde instellingen: 
>
> * Als u de Azure CLI gebruikt, moet  u de volgende  instellingen opslaan omProtectedSettings.jsaan tePublicSettings.jsde voorgaande voorbeeldopdracht te gebruiken. 
> * Als u PowerShell gebruikt, moet u de volgende instellingen opslaan in `$protectedSettings` en door uit te `$publicSettings` `$protectedSettings = '{ ... }'` gaan.

### <a name="protected-settings"></a>Beveiligde instellingen

De beveiligde instellingen configureren:

* Een opslagaccount.
* Een overeenkomend SAS-token voor het account.
* Verschillende sinks ( `JsonBlob` of `EventHub` met SAS-tokens).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Openbare instellingen

De openbare instellingen zorgen ervoor dat LAD:

* Upload metrische gegevens voor percentage processortijd en metrische gegevens over de gebruikte schijfruimte naar de `WADMetrics*` tabel,
* Upload berichten van de syslog-faciliteit `"user"` en de ernst naar de `"info"` `LinuxSyslog*` tabel.
* Upload de toegevoegd regels in het bestand `/var/log/myladtestlog` naar de `MyLadTestLog` tabel.

In elk geval worden gegevens ook geüpload naar:

* Azure Blob Storage. De containernaam is zoals gedefinieerd in de `JsonBlob` sink.
* Een Event Hubs eindpunt, zoals opgegeven in de `EventHubs` sink.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

De `resourceId` in de configuratie moet overeenkomen met die van de virtuele machine of de virtuele-machineschaalset.

* Het in kaart brengen en waarschuwen van metrische gegevens van het Azure-platform kent de van de `resourceId` VM waarmee u werkt. Er wordt verwacht dat de gegevens voor uw VM worden gevonden met behulp van `resourceId` de opzoeksleutel.
* Als u Automatisch schalen van Azure gebruikt, moet de in de configuratie `resourceId` voor automatisch schalen overeenkomen met de `resourceId` die lad gebruikt.
* De `resourceId` is ingebouwd in de namen van JSON-blobs die zijn geschreven door LAD.

## <a name="view-your-data"></a>Uw gegevens weergeven

Gebruik de Azure Portal prestatiegegevens weer te geven of waarschuwingen in te stellen:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Schermopname van de Azure Portal. De schijfruimte gebruikt voor metrische gegevens is geselecteerd. De resulterende grafiek wordt weergegeven.":::

De `performanceCounters` gegevens worden altijd opgeslagen in een Azure Storage tabel. Azure Storage API's zijn beschikbaar voor veel talen en platformen.

Gegevens die naar `JsonBlob` sinks worden verzonden, worden opgeslagen in blobs in het opslagaccount met de naam in de [beveiligde instellingen](#protected-settings). U kunt de blobgegevens gebruiken in Azure Blob Storage API's.

U kunt deze UI-hulpprogramma's ook gebruiken om toegang te krijgen tot de gegevens in Azure Storage:

* Visual Studio Server Explorer
* [Azure-opslagverkenner](https://azurestorageexplorer.codeplex.com/)

De volgende schermopname van een Azure Storage Explorer-sessie toont de gegenereerde Azure Storage tabellen en containers van een correct geconfigureerde LAD 4.0-extensie op een test-VM. De afbeelding komt niet exact overeen met de [LAD 4.0-voorbeeldconfiguratie](#example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Schermopname van Azure Storage Explorer.":::

Zie de relevante documentatie voor Event Hubs informatie over het gebruik van berichten Event Hubs een [eindpunt.](../../event-hubs/event-hubs-about.md)

## <a name="next-steps"></a>Volgende stappen

* Maak [in Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)waarschuwingen voor de metrische gegevens die u verzamelt.
* [Maak bewakingsgrafieken](../../azure-monitor/data-platform.md) voor uw metrische gegevens.
* [Maak een virtuele-machineschaalset met](../linux/tutorial-create-vmss.md) behulp van uw metrische gegevens om automatisch schalen te bepalen.