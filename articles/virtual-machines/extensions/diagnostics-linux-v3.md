---
title: Azure Compute-Linux Diagnostic extension 3,0
description: De Azure Linux Diagnostic extension (LAD) 3,0 configureren voor het verzamelen van metrische gegevens en logboek gebeurtenissen van virtuele Linux-machines die in Azure worden uitgevoerd.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 6b03d4fb51021019da4c7d31fccf28b0ee157f32
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670537"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Linux Diagnostic extension 3,0 gebruiken voor het bewaken van metrische gegevens en Logboeken

In dit document wordt versie 3,0 en nieuwer van de Linux Diagnostic-extensie beschreven.

> [!IMPORTANT]
> Zie [dit document](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)voor meer informatie over versie 2,3 en ouder.

## <a name="introduction"></a>Introductie

De diagnostische extensie van Linux helpt gebruikers bij het controleren van de status van een virtuele Linux-machine die wordt uitgevoerd op Microsoft Azure. Het heeft de volgende mogelijkheden:

* Hiermee worden metrische gegevens van de systeem prestaties verzameld van de virtuele machine en opgeslagen in een specifieke tabel in een aangewezen opslag account.
* Hiermee worden logboek gebeurtenissen van syslog opgehaald en opgeslagen in een specifieke tabel in het toegewezen opslag account.
* Hiermee kunnen gebruikers de gegevens waarden aanpassen die worden verzameld en geüpload.
* Hiermee kunnen gebruikers de syslog-faciliteiten en ernst niveaus van gebeurtenissen die worden verzameld en geüpload aanpassen.
* Hiermee kunnen gebruikers bepaalde logboek bestanden uploaden naar een aangewezen opslag tabel.
* Ondersteunt het verzenden van metrische gegevens en logboek gebeurtenissen naar wille keurige EventHub-eind punten en blobs in JSON-indeling in het aangewezen opslag account.

Deze uitbrei ding werkt met beide Azure-implementatie modellen.

## <a name="installing-the-extension-in-your-vm"></a>De extensie in uw virtuele machine installeren

U kunt deze uitbrei ding inschakelen met behulp van de Azure PowerShell-cmdlets, Azure CLI-scripts, ARM-sjablonen of de Azure Portal. Zie [Extensions-functies](features-linux.md)voor meer informatie.

>[!NOTE]
>Bepaalde onderdelen van de diagnostische VM-extensie worden ook verzonden in de [log Analytics VM-extensie](./oms-linux.md). Vanwege deze architectuur kunnen er conflicten optreden als beide uitbrei dingen worden geïnstantieerd in dezelfde ARM-sjabloon. Om deze runtime-conflicten te voor komen, gebruikt u de- [ `dependsOn` instructie](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) om te controleren of de uitbrei dingen opeenvolgend zijn geïnstalleerd. De uitbrei dingen kunnen in een van beide volg orde worden geïnstalleerd.

Met deze installatie-instructies en een [Download bare voorbeeld configuratie](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) Lad 3,0 instellen op:

* Leg dezelfde metrische gegevens vast en sla deze op als die zijn geleverd door LAD 2,3.
* Leg een nuttige set bestandssysteem gegevens vast, nieuw in LAD 3,0.
* Leg de standaard syslog-verzameling in die is ingeschakeld door LAD 2,3.
* Schakel de Azure Portal-ervaring in voor grafieken en waarschuwingen voor metrische gegevens van de virtuele machine.

De Download bare configuratie is slechts een voor beeld. Wijzig deze in uw eigen behoeften.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

De diagnostische Linux-extensie ondersteunt de volgende distributies en versies. De lijst met distributies en versies is alleen van toepassing op installatie kopieën voor Linux-leveranciers van Azure. BYOL-en BYOS-installatie kopieën van derden, zoals toestellen, worden in het algemeen niet ondersteund voor de diagnostische extensie van Linux.

Een distributie waarin alleen primaire versies, zoals Debian 7, wordt vermeld, wordt ook voor alle secundaire versies ondersteund. Als er een specifieke secundaire versie is opgegeven, wordt alleen die specifieke versie ondersteund; als "+" is toegevoegd, worden secundaire versies die gelijk zijn aan of groter zijn dan de opgegeven versie worden ondersteund.

Ondersteunde distributies en versies:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6,7 +

### <a name="prerequisites"></a>Vereisten

* **Azure Linux-agent versie 2.2.0 of hoger**. De meeste installatie kopieën van de Azure VM Linux-galerie bevatten versie 2.2.7 of hoger. Voer uit `/usr/sbin/waagent -version` om te controleren of de versie is geïnstalleerd op de VM. Als op de virtuele machine een oudere versie van de gast agent wordt uitgevoerd, volgt u [deze instructies](./update-linux-agent.md) om de app bij te werken.
* **Azure CLI**. [Stel de Azure cli](/cli/azure/install-azure-cli) -omgeving in op uw machine.
* De wget-opdracht als u deze nog niet hebt: Voer uit `sudo apt-get install wget` .
* Een bestaand Azure-abonnement en een bestaand opslag account voor algemeen gebruik voor het opslaan van de gegevens in.  Opslag accounts voor algemeen gebruik ondersteunen tabel opslag die is vereist.  Een Blob Storage-account werkt niet.
* Python 2

### <a name="python-requirement"></a>Python-vereiste

De diagnostische extensie voor Linux vereist python 2. Als uw virtuele machine gebruikmaakt van een distributie die niet standaard Python 2 bevat, moet u deze installeren. Met de volgende voorbeeld opdrachten wordt python 2 op verschillende distributies geïnstalleerd.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SuSE `zypper install -y python2`

Het uitvoer bare bestand van python2 moet zijn alias voor *python*. Hier volgt een methode die u kunt gebruiken om deze alias in te stellen:

1. Voer de volgende opdracht uit om alle bestaande aliassen te verwijderen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Voer de volgende opdracht uit om de alias te maken.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Voorbeeld installatie

> [!NOTE]
> Vul voor een van de voor beelden de juiste waarden in voor de variabelen in de eerste sectie voordat u uitvoert. 

Met de voorbeeld configuratie die in deze voor beelden is gedownload, wordt een set standaard gegevens verzameld en naar Table Storage verzonden. De URL voor de voorbeeld configuratie en de inhoud ervan kunnen worden gewijzigd. In de meeste gevallen moet u een kopie van het JSON-bestand van de Portal-instellingen downloaden en aanpassen aan uw behoeften. vervolgens moet u de eigen versie van het configuratie bestand gebruiken in plaats van dat elke keer dat URL wordt gedownload.

#### <a name="azure-cli-sample"></a>Voor beeld van Azure CLI

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-virtual-machine-scale-set-instance"></a>Voor beeld van Azure CLI voor het installeren van de LAD 3,0-extensie voor de virtuele-machine Scale set-instantie

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
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

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>De extensie-instellingen bijwerken

Nadat u uw beveiligde of open bare instellingen hebt gewijzigd, implementeert u deze op de VM door dezelfde opdracht uit te voeren. Als er iets in de instellingen is gewijzigd, worden de bijgewerkte instellingen naar de extensie verzonden. LAD laadt de configuratie opnieuw en start zichzelf opnieuw op.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migratie van eerdere versies van de uitbrei ding

De meest recente versie van de uitbrei ding is **3,0**. **Oude versies (2. x) zijn afgeschaft en kunnen na 31 juli 2018 niet meer worden gepubliceerd**.

> [!IMPORTANT]
> Deze uitbrei ding bevat een belang rijke wijziging in de configuratie van de uitbrei ding. Er is een dergelijke wijziging aangebracht ter verbetering van de beveiliging van de uitbrei ding; Als gevolg hiervan kan achterwaartse compatibiliteit met 2. x niet worden gehandhaafd. De extensie Publisher voor deze uitbrei ding wijkt af van de uitgever voor de 2. x-versies.
>
> Als u wilt migreren van 2. x naar deze nieuwe versie van de uitbrei ding, moet u de oude extensie verwijderen (onder de naam van de oude uitgever) en vervolgens versie 3 van de uitbrei ding installeren.

Aanbevelingen:

* Installeer de uitbrei ding met automatische secundaire versie-upgrade ingeschakeld.
  * Geef op de virtuele machines van het klassieke implementatie model ' 3. * ' op als de versie als u de extensie installeert via Azure XPLAT CLI of Power shell.
  * Onder virtuele machines van Azure Resource Manager-implementatie model, neemt u ' "autoUpgradeMinorVersion": True ' op in de sjabloon VM-implementatie.
* Gebruik een nieuw/ander opslag account voor LAD 3,0. Er zijn enkele kleine incompatibiliteiten tussen LAD 2,3 en LAD 3,0 die het delen van een account lastige:
  * LAD 3,0 slaat syslog-gebeurtenissen op in een tabel met een andere naam.
  * De counterSpecifier-teken reeksen voor `builtin` metrische gegevens verschillen in LAD 3,0.

## <a name="protected-settings"></a>Beveiligde instellingen

Deze set configuratie-informatie bevat gevoelige informatie die moet worden beveiligd tegen de open bare weer gave, bijvoorbeeld opslag referenties. Deze instellingen worden verzonden naar en opgeslagen door de uitbrei ding in versleutelde vorm.

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
storageAccountName | De naam van het opslag account waarin de gegevens worden geschreven door de extensie.
storageAccountEndPoint | Beschrijving Het eind punt dat de Cloud aanduidt waarin het opslag account zich bevindt. Als deze instelling niet aanwezig is, LAD standaard ingesteld op de open bare Azure-Cloud `https://core.windows.net` . Als u een opslag account in azure Duitsland, Azure Government of Azure China wilt gebruiken, stelt u deze waarde dienovereenkomstig in.
storageAccountSasToken | Een [SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) van het account voor Blob en Table Services ( `ss='bt'` ), dat van toepassing is op containers en objecten ( `srt='co'` ), die toevoegen, maken, lijst, bijwerken en schrijf machtigingen ( `sp='acluw'` ) verleent. Neem de eerste vraag teken (?) *niet* op.
mdsdHttpProxy | Beschrijving Er zijn HTTP-proxy gegevens nodig om de extensie in te scha kelen om verbinding te maken met het opgegeven opslag account en eind punt.
sinksConfig | Beschrijving Details van alternatieve doelen waarop metrische gegevens en gebeurtenissen kunnen worden geleverd. De specifieke details van elke gegevens sink die wordt ondersteund door de uitbrei ding, worden behandeld in de volgende secties.

Gebruik de functie **listAccountSas** om een SAS-token in een resource manager-sjabloon op te halen. Zie voor beeld van een voorbeeld sjabloon [lijst functie](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

U kunt eenvoudig de vereiste SAS-token maken via de Azure Portal.

1. Selecteer het opslag account voor algemeen gebruik waarnaar u de extensie wilt schrijven
1. Selecteer ' hand tekening voor gedeelde toegang ' in het gedeelte instellingen van het menu links
1. Maak de juiste secties zoals eerder beschreven
1. Klik op de knop SAS genereren.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Scherm afbeelding toont de pagina gedeelde toegangs handtekening met S A S.":::

Kopieer de gegenereerde SA'S naar het veld storageAccountSasToken. Verwijder de toonaangevende vraag teken (?).

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

In deze optionele sectie worden extra bestemmingen gedefinieerd waarnaar de uitbrei ding de verzamelde informatie verzendt. De Sink-matrix bevat een object voor elke extra gegevens sink. Het kenmerk ' type ' bepaalt de andere kenmerken in het object.

Element | Waarde
------- | -----
naam | Een teken reeks die wordt gebruikt om te verwijzen naar deze Sink elders in de configuratie van de extensie.
type | Het type Sink dat wordt gedefinieerd. Bepaalt de andere waarden (indien van toepassing) in exemplaren van dit type.

Versie 3,0 van de diagnostische Linux-extensie ondersteunt twee Sink-typen: EventHub en JsonBlob.

#### <a name="the-eventhub-sink"></a>De EventHub-Sink

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

De vermelding ' sasURL ' bevat de volledige URL, inclusief SAS-token, voor de Event hub waarnaar de gegevens moeten worden gepubliceerd. LAD vereist een SAS-naamgevings beleid dat de verzend claim mogelijk maakt. Een voor beeld:

* Een Event Hubs naam ruimte maken met de naam `contosohub`
* Een event hub maken in de naam ruimte met de naam `syslogmsgs`
* Een beleid voor gedeelde toegang maken op de Event hub `writer` met de naam waarmee de verzend claim wordt ingeschakeld

Als u een goede SAS hebt gemaakt tot middernacht UTC op 1 januari 2018, kan de waarde van sasURL:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Zie [deze webpagina](/rest/api/eventhub/generate-sas-token#powershell)voor meer informatie over het genereren en ophalen van informatie over SAS-tokens voor Event hubs.

#### <a name="the-jsonblob-sink"></a>De JsonBlob-Sink

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Gegevens die zijn gericht op een JsonBlob-sink, worden opgeslagen in blobs in azure Storage. Elk exemplaar van LAD maakt elk uur voor elke Sink-naam een blob. Elke BLOB bevat altijd een syntactisch geldige JSON-matrix van het object. Nieuwe vermeldingen worden atomisch toegevoegd aan de matrix. Blobs worden opgeslagen in een container met dezelfde naam als de sink. De Azure Storage-regels voor BLOB-container namen zijn van toepassing op de namen van JsonBlob-sinks: tussen 3 en 63 kleine letters, alfanumeriek ASCII-tekens of streepjes.

## <a name="public-settings"></a>Open bare instellingen

Deze structuur bevat verschillende blokken instellingen die de informatie bepalen die door de extensie wordt verzameld. Elke instelling is optioneel. Als u opgeeft `ladCfg` , moet u ook opgeven `StorageAccount` .

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
StorageAccount | De naam van het opslag account waarin de gegevens worden geschreven door de extensie. Moet dezelfde naam zijn als is opgegeven in de [beveiligde instellingen](#protected-settings).
mdsdHttpProxy | Beschrijving Hetzelfde als in de [beveiligde instellingen](#protected-settings). De open bare waarde wordt overschreven door de privé waarde, indien ingesteld. Sla proxy-instellingen die een geheim bevatten, zoals een wacht woord, op in de [beveiligde instellingen](#protected-settings).

De resterende elementen worden gedetailleerd beschreven in de volgende secties.

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

Deze optionele structuur bepaalt het verzamelen van metrische gegevens en logboeken voor levering aan de service Azure metrics en andere gegevens-Sinks. U moet ofwel `performanceCounters` of `syslogEvents` beide opgeven. U moet de `metrics` structuur opgeven.

Element | Waarde
------- | -----
eventVolume | Beschrijving Hiermee bepaalt u het aantal partities dat in de opslag tabel is gemaakt. Moet een van `"Large"` , `"Medium"` of zijn `"Small"` . Als dat niet is opgegeven, is de standaard waarde `"Medium"` .
sampleRateInSeconds | Beschrijving Het standaard interval tussen het verzamelen van metrische gegevens (niet-geaggregeerde). De kleinste ondersteunde sample frequentie is 15 seconden. Als dat niet is opgegeven, is de standaard waarde `15` .

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
resourceId | De Azure Resource Manager Resource-ID van de virtuele machine of van de VM-schaalset waartoe de VM behoort. Deze instelling moet ook worden opgegeven als een JsonBlob-sink wordt gebruikt in de configuratie.
scheduledTransferPeriod | De frequentie waarmee statistische gegevens worden berekend en worden overgebracht naar de metrische gegevens van Azure, uitgedrukt als een 8601-tijds interval. De kleinste overdrachts periode is 60 seconden, dat wil zeggen, PT1M. U moet ten minste één scheduledTransferPeriod opgeven.

Voor beelden van de metrische gegevens die zijn opgegeven in de sectie Performance Counters worden elke 15 seconden verzameld of op basis van de sampling frequentie die expliciet voor de teller is gedefinieerd. Als er meerdere scheduledTransferPeriod-frequenties worden weer gegeven (zoals in het voor beeld), wordt elke aggregatie afzonderlijk berekend.

#### <a name="performancecounters"></a>Performance Counters

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

Deze optionele sectie bepaalt het verzamelen van metrische gegevens. Onbewerkte voor beelden worden geaggregeerd voor elke [scheduledTransferPeriod](#metrics) om deze waarden te produceren:

* gemiddeld
* minimum
* maximum
* laatst verzamelde waarde
* aantal onbewerkte voor beelden dat wordt gebruikt voor het berekenen van de samen voeging

Element | Waarde
------- | -----
wastafel | Beschrijving Een door komma's gescheiden lijst met de namen van de sinks waarnaar LAD geaggregeerde metrische resultaten verzendt. Alle geaggregeerde metrische gegevens worden gepubliceerd naar elke vermelde sink. Zie [sinksConfig](#sinksconfig). Bijvoorbeeld: `"EHsink1, myjsonsink"`.
type | Identificeert de werkelijke provider van de metriek.
klasse | Samen met "Counter" identificeert de specifieke metriek binnen de naam ruimte van de provider.
counter | Samen met "class" identificeert de specifieke metriek binnen de naam ruimte van de provider.
counterSpecifier | Identificeert de specifieke metrische waarde binnen de metrische naam ruimte van Azure.
regeling | Beschrijving Selecteert een specifiek exemplaar van het object waarop de metriek van toepassing is of selecteert de aggregatie voor alle exemplaren van dat object. Zie metrische definities voor meer informatie `builtin` .
sampleRate | IS 8601-interval waarmee de snelheid wordt ingesteld waarmee onbewerkte voor beelden voor deze metrische gegevens worden verzameld. Als deze niet is ingesteld, wordt het verzamelings interval ingesteld met de waarde [sampleRateInSeconds](#ladcfg). De kortste ondersteunde sample frequentie is 15 seconden (PT15S).
eenheid | Moet een van de volgende teken reeksen zijn: "Count", "bytes", "seconden", "percent", "CountPerSecond", "BytesPerSecond", "milliseconde". Hiermee definieert u de eenheid voor de metriek. Consumenten van de verzamelde gegevens verwachten dat de verzamelde gegevens waarden overeenkomen met deze eenheid. Dit veld wordt door LAD genegeerd.
displayName | Het label (in de taal die is opgegeven door de instellingen van de bijbehorende land instellingen) dat aan deze gegevens in azure-metrieken moet worden gekoppeld. Dit veld wordt door LAD genegeerd.

De counterSpecifier is een wille keurige id. Consumenten van metrische gegevens, zoals het Azure Portal grafiek-en waarschuwings onderdeel, gebruiken counterSpecifier als de "sleutel" waarmee een metriek of een exemplaar van een metriek wordt aangeduid. Voor `builtin` metrische gegevens raden we u aan om counterSpecifier-waarden te gebruiken die beginnen met `/builtin/` . Als u een specifiek exemplaar van een metriek verzamelt, raden we u aan de id van het exemplaar te koppelen aan de waarde counterSpecifier. Een aantal voorbeelden:

* `/builtin/Processor/PercentIdleTime` -Niet-actieve tijd, gemiddeld in alle Vcpu's
* `/builtin/Disk/FreeSpace(/mnt)` -Beschik bare ruimte voor het/mnt-bestands systeem
* `/builtin/Disk/FreeSpace` -Beschik bare ruimte op alle gekoppelde bestands systemen

LAD en de Azure Portal verwacht dat de counterSpecifier-waarde overeenkomt met een patroon. Wees consistent met het samen stellen van counterSpecifier-waarden.

Wanneer u opgeeft `performanceCounters` , schrijft Lad altijd gegevens naar een tabel in azure Storage. U kunt dezelfde gegevens schrijven naar JSON-blobs en/of Event Hubs, maar u kunt het opslaan van gegevens naar een tabel niet uitschakelen. Alle exemplaren van de diagnostische uitbrei ding die is geconfigureerd voor gebruik van dezelfde opslag accountnaam en-eind punt, voegen hun metrische gegevens en Logboeken toe aan dezelfde tabel. Als er te veel virtuele machines naar dezelfde tabel partitie schrijven, kan Azure de schrijf bewerkingen naar die partitie beperken. De instelling eventVolume zorgt ervoor dat vermeldingen worden verdeeld over 1 (klein), 10 (gemiddeld) of 100 (grote) verschillende partities. Normaal gesp roken is ' medium ' voldoende om ervoor te zorgen dat verkeer niet wordt beperkt. De functie voor Azure-metrieken van de Azure Portal gebruikt de gegevens in deze tabel om grafieken te maken of om waarschuwingen te activeren. De tabel naam is de samen voeging van deze teken reeksen:

* `WADMetrics`
* De "scheduledTransferPeriod" voor de geaggregeerde waarden die in de tabel zijn opgeslagen
* `P10DV2S`
* Een datum, in de vorm "JJJMMDD", die elke 10 dagen verandert

Voor beelden zijn `WADMetricsPT1HP10DV2S20170410` en `WADMetricsPT1MP10DV2S20170609` .

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

Deze optionele sectie bepaalt het verzamelen van logboek gebeurtenissen van syslog. Als de sectie wordt wegge laten, worden syslog-gebeurtenissen helemaal niet vastgelegd.

De syslogEventConfiguration-verzameling heeft één vermelding voor elke syslog-faciliteit. Als minSeverity is voor een bepaalde faciliteit, of als deze faciliteit niet in het-element wordt weer gegeven, worden er geen gebeurtenissen vastgelegd.

Element | Waarde
------- | -----
wastafel | Een door komma's gescheiden lijst met de namen van de sinks waarnaar afzonderlijke logboek gebeurtenissen worden gepubliceerd. Alle logboek gebeurtenissen die overeenkomen met de beperkingen in syslogEventConfiguration, worden gepubliceerd naar elke vermelde sink. Voor beeld: "EHforsyslog"
facilityName | De naam van een syslog-faciliteit (bijvoorbeeld ' LOG \_ User ' of ' Log \_ LOCAL0 '). Zie de sectie ' faciliteit ' op de [pagina syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html) voor de volledige lijst.
minSeverity | Een niveau van syslog-Ernst (zoals ' logboek \_ fout ' of ' logboek \_ gegevens '). Zie de sectie ' niveau ' van de [pagina syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html) voor de volledige lijst. De extensie legt gebeurtenissen vast die zijn verzonden naar de faciliteit op of boven het opgegeven niveau.

Wanneer u opgeeft `syslogEvents` , schrijft Lad altijd gegevens naar een tabel in azure Storage. U kunt dezelfde gegevens schrijven naar JSON-blobs en/of Event Hubs, maar u kunt het opslaan van gegevens naar een tabel niet uitschakelen. Het partitioneren van deze tabel is hetzelfde als de beschrijving voor `performanceCounters` . De tabel naam is de samen voeging van deze teken reeksen:

* `LinuxSyslog`
* Een datum, in de vorm "JJJMMDD", die elke 10 dagen verandert

Voor beelden zijn `LinuxSyslog20170410` en `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Deze optionele sectie bepaalt de uitvoering van wille keurige [Omi](https://github.com/Microsoft/omi) -query's.

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
naamruimte | Beschrijving De OMI-naam ruimte waarbinnen de query moet worden uitgevoerd. Als u deze niet opgeeft, is de standaard waarde ' root/SCx ', geïmplementeerd door de [System Center-providers voor meerdere platforms](https://github.com/Microsoft/SCXcore).
query | De OMI-query die moet worden uitgevoerd.
tabel | Beschrijving De Azure Storage-tabel, in het toegewezen opslag account (Zie [beveiligde instellingen](#protected-settings)).
frequency | Beschrijving Het aantal seconden tussen de uitvoering van de query. De standaard waarde is 300 (5 minuten); de minimum waarde is 15 seconden.
wastafel | Beschrijving Een door komma's gescheiden lijst met namen van extra sinks waarmee de resultaten van onbewerkte voorbeeld gegevens moeten worden gepubliceerd. Geen aggregatie van deze onbewerkte voor beelden wordt berekend door de uitbrei ding of door de metrische gegevens van Azure.

U moet ' Table ' of ' sinks ' of beide opgeven.

### <a name="filelogs"></a>fileLogs

Hiermee bepaalt u het vastleggen van logboek bestanden. LAD legt nieuwe tekst regels vast wanneer ze naar het bestand worden geschreven en schrijft ze naar tabel rijen en/of naar een opgegeven Sink (JsonBlob of EventHub).

> [!NOTE]
> fileLogs worden vastgelegd door een subonderdeel van LAD met de naam `omsagent` . Als u fileLogs wilt verzamelen, moet u ervoor zorgen dat de `omsagent` gebruiker lees machtigingen heeft voor de bestanden die u opgeeft, evenals machtigingen voor uitvoeren voor alle mappen in het pad naar dat bestand. U kunt dit controleren door uit te voeren `sudo su omsagent -c 'cat /path/to/file'` nadat Lad is geïnstalleerd.

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
file | De volledige padnaam van het logboek bestand dat moet worden bekeken en vastgelegd. De padnaam moet een naam hebben van één bestand; de naam van een map kan niet worden genoemd of joker tekens bevatten. Het gebruikers account ' omsagent ' moet lees toegang hebben tot het bestandspad.
tabel | Beschrijving De Azure Storage-tabel, in het toegewezen opslag account (zoals opgegeven in de beveiligde configuratie), waarin nieuwe regels van de "staart" van het bestand worden geschreven.
wastafel | Beschrijving Een door komma's gescheiden lijst met namen van extra sinks waarnaar logboek regels worden verzonden.

U moet ' Table ' of ' sinks ' of beide opgeven.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metrische gegevens die worden ondersteund door de ingebouwde provider

De ingebouwde metrische waarde-provider is een bron van metrische gegevens die het interessantst zijn voor een groot aantal gebruikers. Deze metrische gegevens zijn onderverdeeld in vijf algemene klassen:

* Processor
* Geheugen
* Netwerk
* Bestandssysteem
* Schijf

### <a name="builtin-metrics-for-the-processor-class"></a>ingebouwde metrische gegevens voor de processor klasse

De processor klasse van metrische gegevens biedt informatie over het processor gebruik in de virtuele machine. Bij het samen voegen van percentages is het resultaat het gemiddelde voor alle Cpu's. Als in een virtuele machine met twee vCPU een vCPU van 100% bezet was en de andere 100% inactief was, zou de gerapporteerde PercentIdleTime 50 zijn. Als elk vCPU voor dezelfde periode 50% was, zou het gerapporteerde resultaat ook 50 zijn. In een vier vCPU-VM, met één vCPU 100% bezet en de andere inactief, is de gerapporteerde PercentIdleTime 75.

counter | Betekenis
------- | -------
PercentIdleTime | Het percentage van de tijd tijdens het aggregatie venster dat door de processor is uitgevoerd. de kernel is niet actief
PercentProcessorTime | Percentage tijd voor het uitvoeren van een niet-inactieve thread
PercentIOWaitTime | Het percentage tijd dat wacht op het volt ooien van i/o-bewerkingen
PercentInterruptTime | Percentage tijd voor het uitvoeren van hardware/software-interrupts en Dpc's (uitgestelde procedure aanroepen)
PercentUserTime | Van niet-actieve tijd tijdens het aggregatie venster, het percentage tijd besteed aan de gebruiker met de normale prioriteit
PercentNiceTime | Niet-actieve tijd, het percentage dat is besteed aan een lagere prioriteit
PercentPrivilegedTime | Niet-actieve tijd, het percentage dat is uitgegeven in de bevoegde modus (kernel)

De eerste vier prestatie meter items moeten worden opgeteld bij 100%. De laatste drie tellers zijn ook opgeteld bij 100%; ze onderverdelen de som van PercentProcessorTime, PercentIOWaitTime en PercentInterruptTime.

Stel in om een enkele metrische waarde voor alle processors te verkrijgen `"condition": "IsAggregate=TRUE"` . Stel in om een metrische waarde voor een specifieke processor te verkrijgen, zoals de tweede logische processor van een virtuele machine met vier vCPU `"condition": "Name=\\"1\\""` . De logische processor nummers bevinden zich in het bereik `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>ingebouwde metrische gegevens voor de klasse Memory

De geheugen klasse van metrische gegevens bevat informatie over het geheugen gebruik, paging en wisselen.

counter | Betekenis
------- | -------
AvailableMemory | Beschikbaar fysiek geheugen in MiB
PercentAvailableMemory | Beschikbaar fysiek geheugen als percentage van het totale geheugen
UsedMemory | Fysiek geheugen (MiB) in gebruik
PercentUsedMemory | Fysiek geheugen in gebruik als percentage van het totale geheugen
PagesPerSec | Totale paginering (lezen/schrijven)
PagesReadPerSec | Pagina's van het back-uparchief (wissel bestand, programma bestand, toegewezen bestand enz.)
PagesWrittenPerSec | Pagina's die zijn geschreven naar een back-up van de opslag (wissel bestand, toegewezen bestand enzovoort)
AvailableSwap | Ongebruikte wissel ruimte (MiB)
PercentAvailableSwap | Ongebruikte wissel ruimte als percentage van de totale swap
UsedSwap | Wissel ruimte in gebruik (MiB)
PercentUsedSwap | Wissel ruimte in gebruik als percentage van de totale swap

Deze klasse met metrische gegevens heeft slechts één exemplaar. Het kenmerk condition heeft geen bruikbare instellingen en moet worden wegge laten.

### <a name="builtin-metrics-for-the-network-class"></a>ingebouwde metrische gegevens voor de netwerk klasse

De netwerk klasse van meet gegevens biedt informatie over netwerk activiteit op een afzonderlijke netwerk interface sinds het opstarten. LAD maakt geen metrische gegevens over de band breedte beschikbaar, die kunnen worden opgehaald uit de metrische gegevens van de host.

counter | Betekenis
------- | -------
BytesTransmitted | Totaal aantal verzonden bytes sinds opstarten
BytesReceived | Totaal aantal ontvangen bytes sinds opstarten
BytesTotal | Totaal aantal verzonden of ontvangen bytes sinds opstarten
PacketsTransmitted | Totaal aantal verzonden pakketten sinds opstarten
PacketsReceived | Totaal aantal ontvangen pakketten sinds opstarten
TotalRxErrors | Aantal ontvangst fouten sinds opstarten
TotalTxErrors | Aantal verzend fouten sinds het opstarten
TotalCollisions | Aantal conflicten dat is gerapporteerd door de netwerk poorten sinds het opstarten

 Hoewel deze klasse wordt instantied, biedt LAD geen ondersteuning voor het vastleggen van netwerk metrieken die zijn geaggregeerd op alle netwerk apparaten. Voor het verkrijgen van de metrische gegevens voor een specifieke interface, zoals eth0, set `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>ingebouwde metrische gegevens voor de klasse File System

De klasse File System van metrische gegevens bevat informatie over het gebruik van het bestands systeem. Absolute en percentage waarden worden gerapporteerd zoals ze zouden worden weer gegeven voor een gewone gebruiker (niet root).

counter | Betekenis
------- | -------
FreeSpace | Beschik bare schijf ruimte in bytes
UsedSpace | Gebruikte schijf ruimte in bytes
PercentFreeSpace | Percentage beschik bare ruimte
PercentUsedSpace | Percentage gebruikte ruimte
PercentFreeInodes | Percentage ongebruikte inodes
PercentUsedInodes | Het percentage toegewezen (in gebruik) inodes dat is opgeteld voor alle bestands systemen
BytesReadPerSecond | Gelezen bytes per seconde
BytesWrittenPerSecond | Geschreven bytes per seconde
BytesPerSecond | Gelezen of geschreven bytes per seconde
ReadsPerSecond | Lees bewerkingen per seconde
WritesPerSecond | Schrijf bewerkingen per seconde
TransfersPerSecond | Lees-of schrijf bewerkingen per seconde

Geaggregeerde waarden voor alle bestands systemen kunnen worden verkregen door in te stellen `"condition": "IsAggregate=True"` . Waarden voor een specifiek gekoppeld bestands systeem, zoals '/mnt ', kunnen worden verkregen door in te stellen `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Als u de Azure Portal in plaats van JSON gebruikt, is het veld met de juiste voorwaarde waarde name = '/mnt '

### <a name="builtin-metrics-for-the-disk-class"></a>ingebouwde metrische gegevens voor de klasse schijf

De klasse schijf van metrische gegevens bevat informatie over het gebruik van schijf apparaten. Deze statistieken zijn van toepassing op het hele station. Als er meerdere bestands systemen op een apparaat zijn, worden de prestatie meter items voor dat apparaat in feite geaggregeerd.

counter | Betekenis
------- | -------
ReadsPerSecond | Lees bewerkingen per seconde
WritesPerSecond | Schrijf bewerkingen per seconde
TransfersPerSecond | Totaal aantal bewerkingen per seconde
AverageReadTime | Gemiddeld aantal seconden per Lees bewerking
AverageWriteTime | Gemiddeld aantal seconden per schrijf bewerking
AverageTransferTime | Gemiddeld aantal seconden per bewerking
AverageDiskQueueLength | Gemiddeld aantal schijf bewerkingen in de wachtrij
ReadBytesPerSecond | Aantal gelezen bytes per seconde
WriteBytesPerSecond | Aantal geschreven bytes per seconde
BytesPerSecond | Aantal gelezen of geschreven bytes per seconde

Geaggregeerde waarden voor alle schijven kunnen worden verkregen door in te stellen `"condition": "IsAggregate=True"` . Als u informatie wilt ophalen voor een specifiek apparaat (bijvoorbeeld/dev/sdf1), stelt u in `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>LAD 3,0 installeren en configureren

### <a name="azure-cli"></a>Azure CLI

Als uw beveiligde instellingen zich in het bestand bevinden ProtectedSettings.jsop en uw open bare configuratie-informatie zich in PublicSettings.jsbevindt, voert u de volgende opdracht uit:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

De opdracht gaat ervan uit dat u gebruikmaakt van de Azure-resource beheer modus van de Azure CLI. Als u LAD wilt configureren voor Vm's van het klassieke implementatie model (ASM), schakelt u over naar de modus ASM ( `azure config mode asm` ) en laat u de naam van de resource groep weg in de opdracht. Zie de [documentatie over PLATFORMOVERSCHRIJDENDE cli](/cli/azure/authenticate-azure-cli)voor meer informatie.

### <a name="powershell"></a>PowerShell

Ervan uitgaande dat uw beveiligde instellingen in de `$protectedSettings` variabele staan en dat uw open bare configuratie gegevens in de `$publicSettings` variabele staan, voert u de volgende opdracht uit:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Een voor beeld van een LAD-configuratie van 3,0

Op basis van de voor gaande definities ziet u hier een voor beeld van een LAD-extensie configuratie van 3,0 met enkele uitleg. Als u dit voor beeld wilt Toep assen op uw aanvraag, moet u uw eigen opslag accountnaam, SAS-token voor het account en Event hubs SAS-tokens gebruiken.

> [!NOTE]
> Afhankelijk van of u de Azure CLI of Power shell gebruikt om LAD te installeren, is de methode voor het bieden van open bare en beveiligde instellingen anders. Als u de Azure CLI gebruikt, slaat u de volgende instellingen op ProtectedSettings.jsop en PublicSettings.jsu in om te gebruiken met de bovenstaande voorbeeld opdracht. Als u Power shell gebruikt, slaat u de instellingen op en wilt u deze `$protectedSettings` `$publicSettings` uitvoeren `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Beveiligde instellingen

Deze beveiligde instellingen configureren:

* een opslag account
* een overeenkomend account SAS-token
* verschillende Sinks (JsonBlob of event hubs met SAS-tokens)

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

### <a name="public-settings"></a>Open bare instellingen

Deze open bare instellingen veroorzaken LAD:

* De gegevens voor het percentage van processor tijd en gebruikte schijf ruimte uploaden naar de `WADMetrics*` tabel
* Berichten van de syslog-faciliteit ' gebruiker ' en de ernst ' info ' uploaden naar de `LinuxSyslog*` tabel
* OMI-query resultaten (PercentProcessorTime en PercentIdleTime) uploaden naar de benoemde `LinuxCPU` tabel
* Toegevoegde regels in het bestand uploaden `/var/log/myladtestlog` naar de `MyLadTestLog` tabel

In elk geval worden gegevens ook geüpload naar:

* Azure Blob-opslag (container naam is zoals gedefinieerd in de JsonBlob-Sink)
* Event hubs-eind punt (zoals opgegeven in de Event hubs-Sink)

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

De `resourceId` in de configuratie moet overeenkomen met die van de VM of de schaalset van de virtuele machine.

* De metrische gegevens en waarschuwingen van het Azure-platform hebben een idee van de resourceId van de VM waaraan u werkt. Het verwacht de gegevens voor uw virtuele machine te vinden met de opdracht resourceId de zoek sleutel.
* Als u Azure automatisch schalen gebruikt, moet de resourceId in de configuratie voor automatisch schalen overeenkomen met de resourceId die wordt gebruikt door LAD.
* De resourceId is ingebouwd in de namen van JsonBlobs die zijn geschreven door LAD.

## <a name="view-your-data"></a>Uw gegevens weergeven

Gebruik de Azure Portal om prestatie gegevens weer te geven of waarschuwingen in te stellen:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Scherm afbeelding toont de Azure Portal met de gebruikte schijf ruimte op de geselecteerde metrische gegevens en de resulterende grafiek.":::

De `performanceCounters` gegevens worden altijd opgeslagen in een Azure Storage tabel. Azure Storage-Api's zijn beschikbaar voor veel talen en platforms.

Gegevens die worden verzonden naar JsonBlob-sinks, worden opgeslagen in blobs in het opslag account met de naam in de [beveiligde instellingen](#protected-settings). U kunt de BLOB-gegevens gebruiken met behulp van Azure Blob Storage-Api's.

Daarnaast kunt u deze hulpprogram ma's voor de gebruikers interface gebruiken om toegang te krijgen tot de gegevens in Azure Storage:

* Visual Studio Server Explorer.
* [Scherm opname toont containers en tabellen in azure Storage Explorer.](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Deze moment opname van een Microsoft Azure Storage Explorer-sessie toont de gegenereerde Azure Storage tabellen en containers van een correct geconfigureerde LAD 3,0-extensie op een test-VM. De installatie kopie komt niet exact overeen met de voor [beeld-LAD 3,0-configuratie](#an-example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Scherm afbeelding toont de Azure Storage Explorer.":::


Raadpleeg de relevante [Event hubs-documentatie](../../event-hubs/event-hubs-about.md) voor meer informatie over het gebruiken van berichten die zijn gepubliceerd op een event hubs-eind punt.

## <a name="next-steps"></a>Volgende stappen

* Maak metrische waarschuwingen in [Azure monitor](../../azure-monitor/alerts/alerts-classic-portal.md) voor de metrische gegevens die u verzamelt.
* Maak [bewakings grafieken](../../azure-monitor/data-platform.md) voor uw metrische gegevens.
* Informatie over het [maken van een schaalset voor virtuele machines](../linux/tutorial-create-vmss.md) met behulp van uw metrische gegevens voor het beheren van automatisch schalen.