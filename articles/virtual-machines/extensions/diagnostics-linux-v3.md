---
title: Azure Compute - Diagnostische Linux-extensie 3.0
description: De Azure Linux Diagnostic Extension (LAD) 3.0 configureren voor het verzamelen van metrische gegevens en logboekgebeurtenissen van Linux-VM's die worden uitgevoerd in Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479250"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Diagnostische Linux-extensie 3.0 gebruiken om metrische gegevens en logboeken te bewaken

In dit document wordt versie 3.0 en hoger van de Diagnostische Linux-extensie (LAD) beschreven.

> [!IMPORTANT]
> Zie Monitor the performance and diagnostic data of a Linux VM (De prestaties en diagnostische gegevens van een [Linux-VM](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)bewaken) voor informatie over versie 2.3 en eerder.

## <a name="introduction"></a>Introductie

Met de diagnostische Linux-extensie kan een gebruiker de status bewaken van een Linux-VM die wordt uitgevoerd op Microsoft Azure. Het biedt de volgende mogelijkheden:

* Verzamelt metrische gegevens over systeemprestaties van de VM en slaat deze op in een specifieke tabel in een aangewezen opslagaccount.
* Hiermee worden logboekgebeurtenissen opgehaald uit syslog en opgeslagen in een specifieke tabel in het aangewezen opslagaccount.
* Hiermee kunnen gebruikers de metrische gegevens aanpassen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers de syslog-faciliteiten en de ernstniveaus aanpassen van gebeurtenissen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers opgegeven logboekbestanden uploaden naar een aangewezen opslagtabel.
* Ondersteunt het verzenden van metrische gegevens en logboekgebeurtenissen Azure Event Hubs eindpunten en blobs in JSON-indeling in het aangewezen opslagaccount.

Deze extensie werkt met beide Azure-implementatiemodellen.

## <a name="install-the-extension-on-a-vm"></a>De extensie installeren op een VM

U kunt de extensie inschakelen met behulp Azure PowerShell cmdlets, Azure CLI-scripts, Azure Broncontrole-sjablonen (ARM-sjablonen) of de Azure Portal. Zie Extensiefuncties voor [meer informatie.](features-linux.md)

>[!NOTE]
>Sommige onderdelen van de LAD VM-extensie worden ook verzonden in de [Log Analytics VM-extensie](./oms-linux.md). Vanwege deze architectuur kunnen er conflicten optreden als beide extensies worden gemaakt in dezelfde ARM-sjabloon. 
>
>Om conflicten tijdens de installatie te voorkomen, gebruikt u de [ `dependsOn` -richtlijn om](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) ervoor te zorgen dat de extensies opeenvolgend worden geïnstalleerd. De extensies kunnen in beide volgorde worden geïnstalleerd.

Deze installatie-instructies en een [downloadbare voorbeeldconfiguratie om](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0 te configureren voor:

* Dezelfde metrische gegevens vastleggen en opslaan als in LAD 2.3.
* Leg een handige set metrische gegevens van het bestandssysteem vast. Deze functionaliteit is nieuw in LAD 3.0.
* Leg de standaardverzameling Syslog vast die LAD 2.3 heeft ingeschakeld.
* Schakel de Azure Portal in voor grafieken en waarschuwingen voor metrische gegevens van VM's.

De downloadbare configuratie is slechts een voorbeeld. Pas deze aan uw behoeften aan.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

LAD ondersteunt de volgende distributies en versies. De lijst met distributies en versies is alleen van toepassing op door Azure goedgekeurde Linux-leveranciers. De extensie biedt over het algemeen geen ondersteuning voor BYOL- en BYOS-afbeeldingen van derden, zoals apparaten.

Een distributie met alleen de belangrijkste versies, zoals Debian 7, wordt ook ondersteund voor alle secundaire versies. Als er een secundaire versie is opgegeven, wordt alleen die versie ondersteund. Als er een plusteken (+) wordt toegevoegd, worden secundaire versies ondersteund die gelijk zijn aan of hoger zijn dan de opgegeven versie.

Ondersteunde distributies en versies:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Vereisten

* **Azure Linux Agent versie 2.2.0 of hoger.** De meeste Azure VM Linux-galerie-afbeeldingen bevatten versie 2.2.7 of hoger. Voer `/usr/sbin/waagent -version` uit om te bevestigen welke versie op de VM is geïnstalleerd. Als op de VM een oudere versie wordt uitgevoerd, werkt [u de gastagent bij.](./update-linux-agent.md)
* De **Azure CLI**. Stel indien nodig [de Azure CLI-omgeving](/cli/azure/install-azure-cli) in op uw computer.
* De **wget-opdracht**. Als u deze nog niet hebt, voer dan `sudo apt-get install wget` uit.
* Een bestaand **Azure-abonnement.** 
* Een bestaand **opslagaccount voor algemeen gebruik voor** het opslaan van de gegevens. Opslagaccounts voor algemeen gebruik moeten tabelopslag ondersteunen. Een Blob Storage-account werkt niet.
* **Python 2.**

### <a name="python-requirement"></a>Python-vereiste

Voor de diagnostische Linux-extensie is Python 2 vereist. Als uw virtuele machine gebruikmaakt van een distributie die standaard geen Python 2 bevat, moet u deze installeren. Met de volgende voorbeeldopdrachten wordt Python 2 geïnstalleerd op verschillende distributies:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

Het `python2` uitvoerbare bestand moet een alias hebben voor *python*. Hier is één methode om deze alias in te stellen:

1. Voer de volgende opdracht uit om bestaande aliassen te verwijderen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Voer de volgende opdracht uit om de alias te maken.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Voorbeeldinstallatie

De voorbeeldconfiguratie die in de volgende voorbeelden wordt gedownload, verzamelt een set standaardgegevens en verzendt deze naar table storage. De URL voor de voorbeeldconfiguratie en de inhoud ervan kunnen worden gewijzigd. 

In de meeste gevallen moet u een kopie van het JSON-bestand met portalinstellingen downloaden en aanpassen aan uw behoeften. Gebruik vervolgens sjablonen of uw eigen automatisering om een aangepaste versie van het configuratiebestand te gebruiken in plaats van elke keer van de URL te downloaden.

> [!NOTE]
> Vul voor de volgende voorbeelden de juiste waarden in voor de variabelen in de eerste sectie voordat u de code gaat uitvoeren. 
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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-voorbeeld voor het installeren van LAD 3.0 op het exemplaar van de virtuele-machineschaalset

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>De extensie-instellingen bijwerken

Nadat u de beveiligde of openbare instellingen hebt gewijzigd, implementeert u deze op de VM door dezelfde opdracht uit te voeren. Als de instellingen zijn gewijzigd, worden de updates verzonden naar de extensie. LAD laadt de configuratie opnieuw en start zichzelf opnieuw op.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migreren vanuit eerdere versies van de extensie

De nieuwste versie van de extensie is *4.0.* 

> [!IMPORTANT]
> Deze extensie introduceert belangrijke wijzigingen in de configuratie. Een dergelijke wijziging heeft de beveiliging van de extensie verbeterd, zodat compatibiliteit met eerdere versie 2.x niet kan worden gehandhaafd. De uitgever van de extensie voor deze extensie verschilt ook van de uitgever voor de 2.x-versies.
>
> Als u wilt migreren van 2.x naar de nieuwe versie, moet u eerst de oude extensie verwijderen (onder de oude naam van de uitgever). Installeer vervolgens versie 3.

Aanbevelingen:

* Installeer de extensie met automatische secundaire versie-upgrade ingeschakeld.
  * Geef op klassieke implementatiemodel-VM's versie op als u de extensie `3.*` installeert via de Azure XPLAT CLI of PowerShell.
  * Neem Azure Resource Manager VM's voor het implementatiemodel `"autoUpgradeMinorVersion": true` op in de VM-implementatiesjabloon.
* Gebruik een nieuw of ander opslagaccount voor LAD 3.0. LAD 2.3 en LAD 3.0 hebben verschillende kleine incompatibiliteit die het delen van een account lastig maken:
  * LAD 3.0 slaat syslog-gebeurtenissen op in een tabel met een andere naam.
  * De `counterSpecifier` tekenreeksen `builtin` voor metrische gegevens verschillen in LAD 3.0.

## <a name="protected-settings"></a>Beveiligde instellingen

Deze set configuratiegegevens bevat gevoelige informatie die moet worden beveiligd tegen openbare weergave. Het bevat bijvoorbeeld opslagreferenties. Deze instellingen worden versleuteld verzonden naar en opgeslagen door de extensie.

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
storageAccountName | De naam van het opslagaccount waarin gegevens worden geschreven door de extensie.
storageAccountEndPoint | (Optioneel) Het eindpunt dat de cloud identificeert waarin het opslagaccount bestaat. Als deze instelling niet aanwezig is, is de LAD-standaard de openbare Cloud van Azure, `https://core.windows.net` . Als u een opslagaccount wilt gebruiken in Azure Duitsland, Azure Government of Azure China 21Vianet, stelt u deze waarde zo nodig in.
storageAccountSasToken | Een [SAS-token voor het account](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) voor blob- en tabelservices ( `ss='bt'` ). Het is van toepassing op containers en objecten ( `srt='co'` ). Er worden machtigingen voor toevoegen, maken, lijst, bijwerken en schrijven verleend ( `sp='acluw'` ). Neem *niet* het vooraanstaand vraagteken (?) op.
mdsdHttpProxy | (Optioneel) HTTP-proxygegevens die de extensie nodig heeft om verbinding te maken met het opgegeven opslagaccount en eindpunt.
sinksConfig | (Optioneel) Details van alternatieve bestemmingen waaraan metrische gegevens en gebeurtenissen kunnen worden geleverd. De volgende secties bevatten informatie over elke gegevenss sink die wordt ondersteund door de extensie.

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

De `sinksConfig` optionele sectie definieert meer bestemmingen naar welke de extensie de informatie verzendt die wordt verzameld. De `sink` matrix bevat een -object voor elke extra gegevenss sink. Het `type` kenmerk bepaalt de andere kenmerken in het -object.

Element | Waarde
------- | -----
naam | Een tekenreeks die ergens anders in de extensieconfiguratie naar deze sink verwijst.
type | Het type sink dat wordt gedefinieerd. Bepaalt de andere waarden (indien van) in exemplaren van dit type.

LAD versie 3.0 ondersteunt twee sinktypen: `EventHub` en `JsonBlob` .

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

De `"sasURL"` vermelding bevat de volledige URL, inclusief het SAS-token, voor de Event Hub waarop gegevens moeten worden gepubliceerd. LAD vereist een SAS om een beleid te noemen waarmee de verzendclaim wordt mogelijk. 

Bijvoorbeeld:

* Maak een Azure Event Hubs naamruimte met de naam `contosohub` .
* Maak een Event Hub in de naamruimte met de naam `syslogmsgs` .
* Maak een beleid voor gedeelde toegang op de Event Hub waarmee de verzendclaim kan worden gemaakt. Noem het beleid `writer` .

Als uw SAS goed is tot middernacht UTC op 1 januari 2018, kan de sasURL-waarde er ongeveer als in dit voorbeeld uit zien:

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

Blobs worden opgeslagen in een container met dezelfde naam als de sink. De Azure Storage voor namen van blobcontainers zijn van toepassing op de namen van `JsonBlob` sinks. De naam moet tussen de 3 en 63 alfanumerieke ASCII-tekens of streepjes in kleine letters bevatten.

## <a name="public-settings"></a>Openbare instellingen

De structuur van de openbare instellingen bevat verschillende instellingenblokken die bepalen welke informatie de extensie verzamelt. Elke instelling is optioneel. Als u `ladCfg` opgeeft, moet u ook `StorageAccount` opgeven.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Waarde
------- | -----
StorageAccount | De naam van het opslagaccount waarin gegevens worden geschreven door de extensie. Dit moet de naam zijn die is opgegeven in de [beveiligde instellingen.](#protected-settings)
mdsdHttpProxy | (Optioneel) Hetzelfde als in de [beveiligde instellingen](#protected-settings). De openbare waarde wordt overschrijven door de persoonlijke waarde, als deze is ingesteld. Plaats proxyinstellingen die een geheim bevatten, zoals een wachtwoord, in de [beveiligde instellingen.](#protected-settings)

De volgende secties bevatten details voor de resterende elementen.

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

De `ladCfg` structuur is optioneel. Het beheert het verzamelen van metrische gegevens en logboeken die worden geleverd aan de Azure Monitor Metrics-service en aan andere gegevenss sinks. U moet het volgende opgeven:

* Of `performanceCounters` of `syslogEvents` beide. 
* De `metrics` structuur.

Element | Waarde
------- | -----
eventVolume | (Optioneel) Hiermee bepaalt u het aantal partities dat in de opslagtabel is gemaakt. Dit moet `"Large"` , `"Medium"` of `"Small"` zijn. Als er geen waarde is opgegeven, is de standaardwaarde `"Medium"` .
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
resourceId | De Azure Resource Manager resource-id van de VM of van de schaalset waar de VM bij hoort. Deze instelling moet ook worden opgegeven als er `JsonBlob` een sink wordt gebruikt in de configuratie.
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
Voorwaarde | (Optioneel) Hiermee selecteert u een specifiek exemplaar van het object waarop de metrische gegevens van toepassing zijn. Of het selecteert de aggregatie voor alle exemplaren van dat object. 
Samplerate | Het IS 8601-interval waarmee de snelheid wordt bepaalt waarmee onbewerkte steekproeven voor deze metrische gegevens worden verzameld. Als de waarde niet is ingesteld, wordt het verzamelingsinterval ingesteld door de waarde van [`sampleRateInSeconds`](#ladcfg) . De kortste ondersteunde steekproeffrequentie is 15 seconden (PT15S).
eenheid | Hiermee definieert u de eenheid voor de metrische gegevens. Moet een van deze tekenreeksen `"Count"` zijn: `"Bytes"` , , , , , , `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Consumenten van de verzamelde gegevens verwachten dat de verzamelde gegevenswaarden overeenkomen met deze eenheid. LAD negeert dit veld.
displayName | Het label dat moet worden gekoppeld aan de gegevens in Azure Monitor Metrics. Dit label is in de taal die is opgegeven door de bijbehorende instelling voor de taal. LAD negeert dit veld.

De `counterSpecifier` is een willekeurige id. Gebruikers van metrische gegevens, zoals de functie Azure Portal grafieken en waarschuwingen, gebruiken als de 'sleutel' waarmee een metrische gegevens of een exemplaar van een metrische `counterSpecifier` gegevens worden geïdentificeerd. 

Voor `builtin` metrische gegevens raden we `counterSpecifier` waarden aan die beginnen met `/builtin/` . Als u een specifiek exemplaar van een metrische waarde verzamelt, raden we u aan de id van het exemplaar aan de waarde te `counterSpecifier` koppelen. 

Hier volgen enkele voorbeelden:

* `/builtin/Processor/PercentIdleTime` - Gemiddelde niet-actieve tijd voor alle vCCPUs
* `/builtin/Disk/FreeSpace(/mnt)` - Vrije ruimte voor het `/mnt` bestandssysteem
* `/builtin/Disk/FreeSpace` - Gemiddelde vrije ruimte voor alle aaneengestelde bestandssystemen

LAD en de Azure Portal verwacht niet dat de `counterSpecifier` waarde overeen komt met een patroon. Wees consistent in de manier waarop u waarden `counterSpecifier` maakt.

Wanneer u `performanceCounters` opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure Storage. Dezelfde gegevens kunnen worden geschreven naar JSON-blobs of Event Hubs of beide. U kunt het opslaan van gegevens in een tabel echter niet uitschakelen. 

Alle exemplaren van LAD die dezelfde opslagaccountnaam en hetzelfde eindpunt gebruiken, voegen hun metrische gegevens en logboeken toe aan dezelfde tabel. Als er te veel VM's naar dezelfde tabelpartitie schrijven, kan Azure schrijfpen naar die partitie benavigeren. 

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
facilityName | De naam van een syslog-faciliteit, zoals `"LOG_USER"` of `"LOG\LOCAL0"` . Zie de sectie Faciliteit op de [pagina syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html)voor meer informatie.
minSeverity | Een ernstniveau voor syslog, zoals `"LOG_ERR"` of `"LOG_INFO"` . Zie de sectie Niveau van de [pagina syslog man voor meer informatie.](http://man7.org/linux/man-pages/man3/syslog.3.html) De extensie legt gebeurtenissen vast die op of boven het opgegeven niveau naar de faciliteit zijn verzonden.

Wanneer u `syslogEvents` opgeeft, schrijft LAD altijd gegevens naar een tabel in Azure Storage. Dezelfde gegevens kunnen worden geschreven naar JSON-blobs of Event Hubs of beide. U kunt het opslaan van gegevens in een tabel echter niet uitschakelen. 

Het partitioneringsgedrag voor de tabel is hetzelfde als beschreven voor `performanceCounters` . De tabelnaam is de samenvoeging van deze tekenreeksen:

* `LinuxSyslog`
* Een datum in de vorm 'YYYYMMDD', die elke 10 dagen wordt gewijzigd

Voorbeelden zijn `LinuxSyslog20170410` en `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

De `perfCfg` sectie is optioneel. Hiermee bepaalt u hoe willekeurige [OPEN Management Infrastructure-query's (OMI) worden](https://github.com/Microsoft/omi) uitgevoerd.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Waarde
------- | -----
naamruimte | (Optioneel) De OMI-naamruimte waarin de query moet worden uitgevoerd. Indien niet gespecificeerd, is de standaardwaarde `"root/scx"` . Het wordt geïmplementeerd door de System Center [platformoverschrijdende providers](https://github.com/Microsoft/SCXcore).
query | De UIT te voeren OMI-query.
tabel | (Optioneel) De Azure Storage tabel in het aangewezen opslagaccount. Zie Beveiligde instellingen [voor meer informatie.](#protected-settings)
frequency | (Optioneel) Het aantal seconden tussen query-runs. De standaardwaarde is 300 (5 minuten). De minimumwaarde is 15 seconden.
Putten | (Optioneel) Een door komma's gescheiden lijst met namen van meer sinks waarop onbewerkte metrische voorbeeldresultaten moeten worden gepubliceerd. Er wordt geen aggregatie van deze onbewerkte voorbeelden berekend door de extensie of door Azure Monitor Metrics.

Of `"table"` `"sinks"` beide moeten worden opgegeven.

### <a name="filelogs"></a>fileLogs

De `fileLogs` sectie bepaalt het vastleggen van logboekbestanden. LAD legt nieuwe tekstregels vast wanneer deze naar het bestand worden geschreven. Deze worden naar tabelrijen en/of opgegeven sinks ( `JsonBlob` of ) `EventHub` wegschreven.

> [!NOTE]
> De `fileLogs` worden vastgelegd door een subcomponent van LAD met de naam `omsagent` . Als u `fileLogs` wilt verzamelen, moet u ervoor `omsagent` zorgen dat de gebruiker leesmachtigingen heeft voor de bestanden die u opgeeft. De gebruiker moet ook uitvoermachtigingen hebben voor alle directories in het pad naar dat bestand. Nadat LAD is geïnstalleerd, kunt u machtigingen controleren door uit te `sudo su omsagent -c 'cat /path/to/file'` gaan.

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
file | De volledige padnaam van het logboekbestand dat moet worden gevolgd en vastgelegd. De padnaam moet één bestand een naam geven. Het mag geen map een naam geven of jokertekens bevatten. Het `omsagent` gebruikersaccount moet leestoegang hebben tot het bestandspad.
tabel | (Optioneel) De Azure Storage waarin nieuwe regels van de 'tail' van het bestand worden geschreven. De tabel moet zich in het aangewezen opslagaccount, zoals opgegeven in de beveiligde configuratie. 
Putten | (Optioneel) Een door komma's gescheiden lijst met namen van meer sinks waar logboekregels naar worden verzonden.

Of `"table"` `"sinks"` , of beide, moet worden opgegeven.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metrische gegevens die worden ondersteund door de ingebouwde provider

De `builtin` provider van metrische gegevens is een bron van metrische gegevens die het interessantst zijn voor een grote groep gebruikers. Deze metrische gegevens vallen in vijf algemene klassen:

* Processor
* Geheugen
* Netwerk
* Bestandssysteem
* Schijf

### <a name="builtin-metrics-for-the-processor-class"></a>builtin metrics for the Processor class

De processorklasse van metrische gegevens bevat informatie over het processorgebruik in de VM. Wanneer percentages worden geaggregeerd, is het resultaat het gemiddelde voor alle CPU's. 

Als in een VM met twee vCPU's één vCPU 100 procent bezet is en de andere 100 procent inactief is, is de `PercentIdleTime` gerapporteerde 50. Als elke vCPU voor dezelfde periode 50 procent bezet is, is het gerapporteerde resultaat ook 50. Wanneer in een VM met vier vCPU's één vCPU voor 100 procent bezet is en de andere VCPU's inactief zijn, is de `PercentIdleTime` gerapporteerde VCPU 75.

Prestatiemeteritem | Betekenis
------- | -------
PercentIdleTime | Percentage tijd tijdens het aggregatievenster dat processors de inactieve kernellus hebben laten uitvoeren
PercentProcessorTime | Percentage tijd dat een niet-inactieve thread wordt uitgevoerd
PercentIOWaitTime | Percentage tijd dat moet worden gewacht tot I/O-bewerkingen zijn uitgevoerd
PercentInterruptTime | Percentage tijd dat hardware- of software-interrupts en DPC's worden uitgevoerd (uitgestelde procedure-aanroepen)
PercentUserTime | Van niet-niet-actieve tijd tijdens het aggregatievenster, het percentage tijd dat in de gebruikersmodus met de normale prioriteit is besteed
PercentNiceTime | Van niet-niet-actieve tijd is het percentage besteed met een lagere (mooie) prioriteit
PercentPrivilegedTime | Van niet-niet-actieve tijd, het percentage dat is besteed in de modus bevoegde (kernel)

De eerste vier tellers moeten 100 procent bedragen. De laatste drie tellers bedragen ook 100 procent. Deze drie tellers verdelen de som van `PercentProcessorTime` , `PercentIOWaitTime` en `PercentInterruptTime` .

Als u één metrische waarde voor alle processors wilt aggregeren, stelt u `"condition": "IsAggregate=TRUE"` in. Als u een metrische gegevens voor een specifieke processor wilt verkrijgen, zoals de tweede logische processor van een VM met vier vCPU's, stelt u `"condition": "Name=\\"1\\""` in. Logische processornummers zijn binnen het bereik `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>ingebouwde metrische gegevens voor de geheugenklasse

De geheugenklasse van metrische gegevens biedt informatie over geheugengebruik, paginering en wisselen.

Prestatiemeteritem | Betekenis
------- | -------
AvailableMemory | Beschikbaar fysiek geheugen in MiB
PercentAvailableMemory | Beschikbaar fysiek geheugen als percentage van het totale geheugen
UsedMemory | In gebruik fysiek geheugen (MiB)
PercentUsedMemory | Fysiek geheugen in gebruik als percentage van het totale geheugen
PagesPerSec | Totaal aantal paginering (lezen/schrijven)
PagesReadPerSec | Pagina's die worden gelezen uit de back-store, zoals wisselbestand, programmabestand en kaartbestand
PagesWrittenPerSec | Pagina's die naar de back-store worden geschreven, zoals wisselbestand en mapped bestand
AvailableSwap | Ongebruikte wisselruimte (MiB)
PercentAvailableSwap | Ongebruikte wisselruimte als percentage van de totale wissel
UsedSwap | In-use wisselruimte (MiB)
PercentUsedSwap | In-use wisselruimte als een percentage van de totale wissel

Deze klasse met metrische gegevens heeft slechts één exemplaar. Het `"condition"` kenmerk heeft geen nuttige instellingen en moet worden weggelaten.

### <a name="builtin-metrics-for-the-network-class"></a>builtin metrics for the Network class

De netwerkklasse van metrische gegevens biedt informatie over netwerkactiviteit op een afzonderlijke netwerkinterface sinds het opstarten. 

LAD maakt geen metrische bandbreedtegegevens beschikbaar. U kunt deze metrische gegevens op halen uit metrische gegevens van de host.

Prestatiemeteritem | Betekenis
------- | -------
BytesTransmitted | Totaal aantal verzonden bytes sinds het opstarten
BytesReceived | Totaal aantal bytes dat is ontvangen sinds het opstarten
BytesTotal | Totaal aantal verzonden of ontvangen bytes sinds het opstarten
PakkettenTransmitted | Totaal aantal verzonden pakketten sinds het opstarten
PakkettenReceived | Totaal aantal ontvangen pakketten sinds het opstarten
TotalRxErrors | Aantal ontvangstfouten sinds het opstarten
TotalTxErrors | Aantal verzendfouten sinds het opstarten
TotalCollisions | Aantal door de netwerkpoorten gerapporteerde aanrijdingen sinds het opstarten

Hoewel de netwerkklasse wordt exemplaar, biedt LAD geen ondersteuning voor het vastleggen van metrische netwerkgegevens die zijn geaggregeerd op alle netwerkapparaten. Als u de metrische gegevens voor een specifieke interface wilt verkrijgen, zoals eth0, stelt u `"condition": "InstanceID=\\"eth0\\""` in.

### <a name="builtin-metrics-for-the-file-system-class"></a>builtin metrics for the File system class

De klasse Bestandssysteem van metrische gegevens bevat informatie over het gebruik van het bestandssysteem. Absolute waarden en percentagewaarden worden gerapporteerd zoals ze worden weergegeven aan een gewone gebruiker (niet hoofdmap).

Prestatiemeteritem | Betekenis
------- | -------
FreeSpace | Beschikbare schijfruimte in bytes
UsedSpace | Gebruikte schijfruimte in bytes
PercentFreeSpace | Percentage vrije ruimte
PercentUsedSpace | Percentage gebruikte ruimte
PercentFreeInodes | Percentage niet-gebruikte indexknooppunten (inodes)
PercentUsedInodes | Percentage toegewezen (in gebruik) inodes dat is opgeteld in alle bestandssystemen
BytesReadPerSecond | Gelezen bytes per seconde
BytesWrittenPerSecond | Geschreven bytes per seconde
BytesPerSecond | Gelezen of geschreven bytes per seconde
ReadsPerSecond | Leesbewerkingen per seconde
WritesPerSecond | Schrijfbewerkingen per seconde
TransfersPerSecond | Lees- of schrijfbewerkingen per seconde

U kunt geaggregeerde waarden voor alle bestandssystemen krijgen door in te `"condition": "IsAggregate=True"` stellen. Haal waarden op voor een specifiek bestandssysteem, zoals `"/mnt"` , door in te `"condition": 'Name="/mnt"'` stellen. 

> [!NOTE]
> Als u in de Azure Portal in plaats van JSON werkt, is het formulier voorwaardeveld `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>ingebouwde metrische gegevens voor de schijfklasse

De schijfklasse van metrische gegevens biedt informatie over het gebruik van schijfapparaat. Deze statistieken zijn van toepassing op het hele station. 

Wanneer een apparaat meerdere bestandssystemen heeft, worden de tellers voor dat apparaat effectief geaggregeerd in alle bestandssystemen.

Prestatiemeteritem | Betekenis
------- | -------
ReadsPerSecond | Leesbewerkingen per seconde
WritesPerSecond | Schrijfbewerkingen per seconde
TransfersPerSecond | Totaal aantal bewerkingen per seconde
AverageReadTime | Gemiddelde seconden per leesbewerking
AverageWriteTime | Gemiddelde seconden per schrijfbewerking
AverageTransferTime | Gemiddeld aantal seconden per bewerking
AverageDiskQueueLength | Gemiddeld aantal schijfbewerkingen in de wachtrij
ReadBytesPerSecond | Aantal bytes gelezen per seconde
WriteBytesPerSecond | Aantal geschreven bytes per seconde
BytesPerSecond | Aantal gelezen of geschreven bytes per seconde

U kunt geaggregeerde waarden op alle schijven krijgen door in te `"condition": "IsAggregate=True"` stellen. Als u informatie voor een specifiek apparaat wilt op halen (bijvoorbeeld `/dev/sdf1` ), stelt u `"condition": "Name=\\"/dev/sdf1\\""` in.

## <a name="install-and-configure-lad-30"></a>LAD 3.0 installeren en configureren

### <a name="azure-cli"></a>Azure CLI

Als uw beveiligde instellingen zich in het bestand *ProtectedSettings.js* op en uw openbare configuratiegegevens zich inPublicSettings.js *op*, voer dan de volgende opdracht uit.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bij de opdracht wordt ervan uitgenomen dat u de Azure Resource Manager van de Azure CLI gebruikt. Als u LAD wilt configureren voor klassieke implementatiemodel-VM's, schakelt u over naar de asm-modus ( ) en laat u de naam van de `azure config mode asm` resourcegroep weg in de opdracht . 

Zie de platformoverschrijdende [CLI-documentatie voor meer informatie.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Als uw beveiligde instellingen zich in de variabele en uw openbare configuratiegegevens in de variabele `$protectedSettings` `$publicSettings` staan, voer dan deze opdracht uit:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Voorbeeld van LAD 3.0-configuratie

Op basis van de voorgaande definities bevat deze sectie een voorbeeld van de LAD 3.0-extensieconfiguratie en een uitleg. Als u dit voorbeeld wilt toepassen op uw case, gebruikt u de naam van uw eigen opslagaccount, SAS-token voor account en Event Hubs SAS-tokens.

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

* Upload metrische gegevens voor percentage processortijd en metrische gegevens over de gebruikte schijfruimte naar de `WADMetrics*` tabel.
* Upload berichten van de syslog-faciliteit `"user"` en de ernst naar de `"info"` `LinuxSyslog*` tabel.
* Upload onbewerkte OMI-queryresultaten ( `PercentProcessorTime` en ) naar de `PercentIdleTime` benoemde `LinuxCPU` tabel.
* Upload de toegevoegd regels in het bestand `/var/log/myladtestlog` naar de `MyLadTestLog` tabel.

In elk geval worden gegevens ook geüpload naar:

* Azure Blob Storage. De containernaam is zoals gedefinieerd in de `JsonBlob` sink.
* Het Event Hubs eindpunt, zoals opgegeven in de `EventHub` sink.

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
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
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

* Het metrische gegevensdiagram van het Azure-platform en waarschuwingen kent de van de `resourceId` VM waarmee u werkt. Er wordt verwacht dat de gegevens voor uw VM worden gevonden met behulp van `resourceId` de opzoeksleutel.
* Als u Automatisch schalen van Azure gebruikt, moet `resourceId` de in de configuratie voor automatisch schalen overeenkomen met de die `resourceId` LAD gebruikt.
* De `resourceId` is ingebouwd in de namen van JSON-blobs die zijn geschreven door LAD.

## <a name="view-your-data"></a>Uw gegevens weergeven

Gebruik de Azure Portal prestatiegegevens weer te geven of om waarschuwingen in te stellen:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Schermopname van de Azure Portal. De schijfruimte Gebruikt voor metrische gegevens is geselecteerd. De resulterende grafiek wordt weergegeven.":::

De `performanceCounters` gegevens worden altijd opgeslagen in een Azure Storage tabel. Azure Storage-API's zijn beschikbaar voor veel talen en platformen.

Gegevens die naar `JsonBlob` sinks worden verzonden, worden opgeslagen in blobs in het opslagaccount met de naam in de [beveiligde instellingen](#protected-settings). U kunt de blobgegevens gebruiken met behulp van Azure Blob Storage API's.

U kunt deze UI-hulpprogramma's ook gebruiken voor toegang tot de gegevens in Azure Storage:

* Visual Studio Server Explorer
* [Azure-opslagverkenner](https://azurestorageexplorer.codeplex.com/)

De volgende schermopname van een Azure Storage Explorer-sessie toont de gegenereerde Azure Storage tabellen en containers van een correct geconfigureerde LAD 3.0-extensie op een test-VM. De afbeelding komt niet exact overeen met de [LAD 3.0-voorbeeldconfiguratie](#example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Schermopname toont Azure Storage Explorer.":::


Zie de relevante documentatie voor Event Hubs informatie over het gebruiken Event Hubs van berichten die zijn gepubliceerd [Event Hubs eindpunt.](../../event-hubs/event-hubs-about.md)

## <a name="next-steps"></a>Volgende stappen

* Maak [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)waarschuwingen voor de metrische gegevens die u verzamelt.
* Maak [bewakingsgrafieken](../../azure-monitor/data-platform.md) voor uw metrische gegevens.
* [Maak een virtuele-machineschaalset met](../linux/tutorial-create-vmss.md) behulp van uw metrische gegevens om automatisch schalen te bepalen.
