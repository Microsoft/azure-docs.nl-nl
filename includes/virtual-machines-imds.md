---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 98866a4f06df0380d52d1aee3eede8aa2f70aaed
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588123"
---
De Azure Instance Metadata Service (IMDS) biedt informatie over instanties van virtuele machines die momenteel worden uitgevoerd. U kunt deze gebruiken om uw virtuele machines te beheren en te configureren.
Deze informatie omvat de SKU, opslag, netwerkconfiguraties en aanstaande onderhoudsgebeurtenissen. Zie Samenvatting van eindpuntcategorieën voor een volledige lijst met [beschikbare gegevens.](#endpoint-categories)

IMDS is beschikbaar voor het uitvoeren van exemplaren van virtuele machines (VM's) en instanties van virtuele-machineschaalsets. Alle eindpunten ondersteunen VM's die zijn gemaakt en beheerd met behulp [van Azure Resource Manager](/rest/api/resources/). Alleen de categorie Attested en het gedeelte Netwerk van de categorie Instantie ondersteunen VM's die zijn gemaakt met behulp van het klassieke implementatiemodel. Het attested-eindpunt doet dit slechts in beperkte mate.

IMDS is een REST API beschikbaar is op een bekend, niet-routeerbaar IP-adres ( `169.254.169.254` ). U hebt alleen toegang tot de VM vanuit de VM. Communicatie tussen de VM en IMDS verlaat nooit de host.
Vraag uw HTTP-clients om web-proxies binnen de VM te omzeilen bij het uitvoeren van query's op IMDS en behandel `169.254.169.254` deze als [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Gebruik

### <a name="access-azure-instance-metadata-service"></a>Toegang tot Azure Instance Metadata Service

Als u toegang wilt krijgen tot IMDS, maakt u een virtuele [Azure Resource Manager](/rest/api/resources/) of de [Azure Portal](https://portal.azure.com)en gebruikt u de volgende voorbeelden.
Zie Azure [Instance Metadata Samples (Voorbeelden van azure-exemplaarmetagegevens) voor meer voorbeelden.](https://github.com/microsoft/azureimds)

Hier is voorbeeldcode voor het ophalen van alle metagegevens voor een exemplaar. Zie Eindpuntcategorieën voor een [](#endpoint-categories) overzicht van alle beschikbare functies voor toegang tot een specifieke gegevensbron.

**Aanvraag**

> [!IMPORTANT]
> In dit voorbeeld worden -proxies overgeslagen. U **moet de** -proxies omzeilen bij het uitvoeren van query's op IMDS. Zie [Proxies](#proxies) voor meer informatie.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld van een antwoord wordt vrij afgedrukt voor de leesbaarheid.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Verificatie en beveiliging

De Instance Metadata Service is alleen toegankelijk vanuit een actief exemplaar van een virtuele machine op een niet-routeerbaar IP-adres. VM's zijn beperkt tot interactie met metagegevens/functionaliteit die betrekking hebben op zichzelf. De API is alleen HTTP en verlaat nooit de host.

Aanvragen om ervoor te zorgen dat aanvragen rechtstreeks zijn bedoeld voor IMDS en om onbedoelde of ongewenste omleiding van aanvragen te voorkomen:
- **Moet** de header bevatten `Metadata: true`
- Mag **geen** `X-Forwarded-For` header bevatten

Elke aanvraag die niet aan beide **vereisten voldoet,** wordt geweigerd door de service.

> [!IMPORTANT]
> IMDS is **geen** kanaal voor gevoelige gegevens. De API is niet-geautheticeerd en is open voor alle processen op de VM. Informatie die via deze service beschikbaar wordt gemaakt, moet worden beschouwd als gedeelde informatie voor alle toepassingen die op de VM worden uitgevoerd.

## <a name="proxies"></a>Proxies

IMDS is **niet bedoeld** om te worden gebruikt achter een proxy en wordt daarom niet ondersteund. De meeste HTTP-clients bieden u de mogelijkheid om proxies voor uw aanvragen uit te schakelen. Deze functionaliteit moet worden gebruikt tijdens de communicatie met IMDS. Raadpleeg de documentatie van uw client voor meer informatie.

> [!IMPORTANT]
> Zelfs als u geen proxyconfiguratie in uw omgeving kent, moet u nog steeds de standaardinstellingen van **de clientproxy overschrijven.** Proxyconfiguraties kunnen automatisch worden ontdekt en als u dergelijke configuraties niet kunt omzeilen, loopt u het risico op uitval als de configuratie van de machine in de toekomst wordt gewijzigd.

## <a name="rate-limiting"></a>Snelheidsbeperking

In het algemeen zijn aanvragen voor IMDS beperkt tot 5 aanvragen per seconde. Aanvragen die deze drempelwaarde overschrijden, worden geweigerd met 429 antwoorden. Aanvragen voor de [categorie Beheerde identiteit](#managed-identity) zijn beperkt tot 20 aanvragen per seconde en 5 gelijktijdige aanvragen.

## <a name="http-verbs"></a>HTTP-woorden

De volgende HTTP-woorden worden momenteel ondersteund:

| Verb | Description |
|------|-------------|
| `GET` | De aangevraagde resource ophalen

## <a name="parameters"></a>Parameters

Eindpunten ondersteunen mogelijk vereiste en/of optionele parameters. Zie [Schema](#schema) en de documentatie voor het specifieke eindpunt in kwestie voor meer informatie.

### <a name="query-parameters"></a>Queryparameters

IMDS-eindpunten ondersteunen HTTP-queryreeksparameters. Bijvoorbeeld: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Hiermee geeft u de parameters:

| Name | Waarde |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Aanvragen met dubbele queryparameternamen worden geweigerd.

### <a name="route-parameters"></a>Routeparameters

Voor sommige eindpunten die grotere JSON-blobs retourneren, ondersteunen we het toepassen van routeparameters aan het aanvraag-eindpunt om te filteren op een subset van het antwoord: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
De parameters komen overeen met de indexen/sleutels die zouden worden gebruikt om het json-object af te lopen tijdens de interactie met een geparseerde weergave.

Retourneert `/metatadata/instance` bijvoorbeeld het json-object:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Als we het antwoord willen filteren op alleen de compute-eigenschap, sturen we de aanvraag: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Op dezelfde manier, als we willen filteren op een geneste eigenschap of specifiek matrixelement, blijven we sleutels gebruiken: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtert op het eerste element van de `Network.interface` eigenschap en retourneren:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Wanneer u filtert op een leaf-knooppunt, `format=json` werkt dit niet. Voor deze query's moet expliciet worden opgegeven omdat de `format=text` standaardindeling json is.

## <a name="schema"></a>Schema

### <a name="data-format"></a>Gegevensindeling

IMDS retourneert standaard gegevens in JSON-indeling ( `Content-Type: application/json` ). Eindpunten die antwoordfiltering ondersteunen (zie [Routeparameters](#route-parameters)) ondersteunen echter ook de indeling `text` .

Als u toegang wilt krijgen tot een antwoordindeling die niet standaard is, geeft u de aangevraagde indeling op als een queryreeksparameter in de aanvraag. Bijvoorbeeld:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

In json-antwoorden zijn alle primitieven van het type en ontbrekende of inappliceerbare waarden zijn altijd opgenomen, maar worden ze ingesteld `string` op een lege tekenreeks.

### <a name="versioning"></a>Versiebeheer

IMDS heeft een versie en het opgeven van de API-versie in de HTTP-aanvraag is verplicht. De enige uitzondering op deze vereiste is [het](#versions) eindpunt van de versies, dat kan worden gebruikt om de beschikbare API-versies dynamisch op te halen.

Als er nieuwere versies worden toegevoegd, zijn oudere versies nog steeds toegankelijk voor compatibiliteit als uw scripts afhankelijk zijn van specifieke gegevensindelingen.

Wanneer u geen versie opgeeft, wordt er een foutbericht weergegeven met een lijst met de nieuwste ondersteunde versies:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Ondersteunde API-versies
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01

### <a name="swagger"></a>Swagger

Een volledige Swagger-definitie voor IMDS is beschikbaar op: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionale beschikbaarheid

De service is **algemeen beschikbaar** in alle Azure Clouds.

## <a name="root-endpoint"></a>Hoofd-eindpunt

Het hoofd-eindpunt is `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Eindpuntcategorieën

De IMDS-API bevat meerdere eindpuntcategorieën die verschillende gegevensbronnen vertegenwoordigen, die elk een of meer eindpunten bevatten. Zie elke categorie voor meer informatie.

| Hoofdmap categorie | Description | Versie geïntroduceerd |
|---------------|-------------|--------------------|
| `/metadata/attested` | Attested [Data (Geattesteerde gegevens) bekijken](#attested-data) | 2018-10-01
| `/metadata/identity` | Zie [Beheerde identiteit via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Zie [Metagegevens van exemplaar](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Zie [Ophalen Load Balancer metagegevens via IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Zie [Scheduled Events via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Zie [Versies](#versions) | N.v.t.

## <a name="versions"></a>Versies

> [!NOTE]
> Deze functie is uitgebracht naast versie 2020-10-01, die momenteel wordt uitgerold en mogelijk nog niet in elke regio beschikbaar is.

### <a name="list-api-versions"></a>API-versies opseenlijst

Retourneert de set ondersteunde API-versies.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parameters

Geen (dit eindpunt is niet omkeerd).

#### <a name="response"></a>Antwoord

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metagegevens van exemplaar

### <a name="get-vm-metadata"></a>VM-metagegevens op halen

Toont de belangrijke metagegevens voor het VM-exemplaar, waaronder rekenkracht, netwerk en opslag. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parameters

| Name | Vereist/optioneel | Beschrijving |
|------|-------------------|-------------|
| `api-version` | Vereist | De versie die wordt gebruikt om de aanvraag te verwerken.
| `format` | Optioneel* | De indeling ( `json` of ) van het `text` antwoord. *Opmerking: kan vereist zijn bij het gebruik van aanvraagparameters

Dit eindpunt ondersteunt het filteren van antwoorden via [routeparameters.](#route-parameters)

#### <a name="response"></a>Antwoord

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Schema-uitsplitsing:

**Compute**

| Gegevens | Description | Versie geïntroduceerd |
|------|-------------|--------------------|
| `azEnvironment` | Azure-omgeving waarin de VM wordt uitgevoerd | 2018-10-01
| `customData` | Deze functie is afgeschaft en uitgeschakeld [in IMDS.](#frequently-asked-questions) Deze is vervangen door `userData` | 2019-02-01
| `evictionPolicy` | Hiermee stelt u in hoe een [spot-VM](../articles/virtual-machines/spot-vms.md) wordt eruit wordt gezet. | 2020-12-01
| `isHostCompatibilityLayerVm` | Identificeert of de VM wordt uitgevoerd op de hostcompatibiliteitslaag | 2020-06-01
| `licenseType` | Type licentie voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Dit is alleen aanwezig voor VM's met AHB-functie | 2020-09-01
| `location` | Azure-regio waarin de VM wordt uitgevoerd | 2017-04-02
| `name` | Naam van de VM | 2017-04-02
| `offer` | Aanbiedingsinformatie voor de VM-afbeelding en is alleen aanwezig voor afbeeldingen die zijn geïmplementeerd vanuit de Azure-afbeeldingsgalerie | 2017-04-02
| `osProfile.adminUsername` | Hiermee geeft u de naam van het beheerdersaccount op | 2020-07-15
| `osProfile.computerName` | Hiermee geeft u de naam van de computer | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Hiermee geeft u op of wachtwoordverificatie is uitgeschakeld. Dit is alleen aanwezig voor Linux-VM's | 2020-10-01
| `osType` | Linux of Windows | 2017-04-02
| `placementGroupId` | [Plaatsingsgroep](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) van uw virtuele-machineschaalset | 2017-08-01
| `plan` | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) met de naam, het product en de uitgever voor een VM als het een Azure Marketplace is | 2018-04-02
| `platformUpdateDomain` |  [Updatedomein](../articles/virtual-machines/availability.md) waarin de VM wordt uitgevoerd | 2017-04-02
| `platformFaultDomain` | [Foutdomein](../articles/virtual-machines/availability.md) waarin de VM wordt uitgevoerd | 2017-04-02
| `priority` | Prioriteit van de VM. Raadpleeg [Spot-VM's](../articles/virtual-machines/spot-vms.md) voor meer informatie | 2020-12-01
| `provider` | Provider van de VM | 2018-10-01
| `publicKeys` | [Verzameling van openbare sleutels die](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) zijn toegewezen aan de VM en paden | 2018-04-02
| `publisher` | Uitgever van de VM-afbeelding | 2017-04-02
| `resourceGroupName` | [Resourcegroep](../articles/azure-resource-manager/management/overview.md) voor uw virtuele machine | 2017-08-01
| `resourceId` | De [volledig gekwalificeerde id](/rest/api/resources/resources/getbyid) van de resource | 2019-03-11
| `sku` | Specifieke SKU voor de VM-afbeelding | 2017-04-02
| `securityProfile.secureBootEnabled` | Identificeert of beveiligd opstarten met UEFI is ingeschakeld op de VM | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Identificeert of de virtuele Trusted Platform Module (TPM) is ingeschakeld op de VM | 2020-06-01
| `storageProfile` | Zie Opslagprofiel hieronder | 2019-06-01
| `subscriptionId` | Azure-abonnement voor de virtuele machine | 2017-08-01
| `tags` | [Tags](../articles/azure-resource-manager/management/tag-resources.md) voor uw virtuele machine  | 2017-08-01
| `tagsList` | Tags die zijn opgemaakt als een JSON-matrix voor eenvoudiger programmatisch parseren  | 2019-06-04
| `userData` | De set gegevens die is opgegeven toen de VM werd gemaakt voor gebruik tijdens of na het inrichten (base64 gecodeerd)  | 2021-01-01
| `version` | Versie van de VM-afbeelding | 2017-04-02
| `vmId` | [Unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de VM | 2017-04-02
| `vmScaleSetName` | [Naam van virtuele-machineschaalset](../articles/virtual-machine-scale-sets/overview.md) van uw virtuele-machineschaalset | 2017-12-01
| `vmSize` | [VM-grootte](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Beschikbaarheidszone](../articles/availability-zones/az-overview.md) van uw virtuele machine | 2017-12-01

**Opslagprofiel**

Het opslagprofiel van een VM is onderverdeeld in drie categorieën: naslaginformatie over de afbeelding, besturingssysteemschijf en gegevensschijven.

Het referentieobject voor de afbeelding bevat de volgende informatie over de afbeelding van het besturingssysteem:

| Gegevens | Description |
|------|-------------|
| `id` | Resource-id
| `offer` | Aanbieding van het platform of de Marketplace-afbeelding
| `publisher` | Uitgever van afbeeldingen
| `sku` | SKU voor afbeeldingen
| `version` | Versie van het platform of de Marketplace-afbeelding

Het object besturingssysteemschijf bevat de volgende informatie over de besturingssysteemschijf die wordt gebruikt door de VM:

| Gegevens | Description |
|------|-------------|
| `caching` | Vereisten voor caching
| `createOption` | Informatie over hoe de VM is gemaakt
| `diffDiskSettings` | Instellingen voor kortstondige schijven
| `diskSizeGB` | Grootte van de schijf in GB
| `image`   | Virtuele harde schijf van brongebruikersafbeelding
| `lun`     | Het nummer van de logische eenheid van de schijf
| `managedDisk` | Parameters voor beheerde schijven
| `name`    | Schijfnaam
| `vhd`     | Virtuele harde schijf
| `writeAcceleratorEnabled` | Of writeAccelerator is ingeschakeld op de schijf

De matrix gegevensschijven bevat een lijst met gegevensschijven die zijn gekoppeld aan de VM. Elk gegevensschijfobject bevat de volgende informatie:

Gegevens | Description |
-----|-------------|
| `caching` | Vereisten voor caching
| `createOption` | Informatie over hoe de VM is gemaakt
| `diffDiskSettings` | Instellingen voor kortstondige schijven
| `diskSizeGB` | Grootte van de schijf in GB
| `encryptionSettings` | Versleutelingsinstellingen voor de schijf
| `image` | Virtuele harde schijf van brongebruikersafbeelding
| `managedDisk` | Parameters voor beheerde schijven
| `name` | Schijfnaam
| `osType` | Type besturingssysteem dat is opgenomen in de schijf
| `vhd` | Virtuele harde schijf
| `writeAcceleratorEnabled` | Of writeAccelerator is ingeschakeld op de schijf

**Netwerk**

| Gegevens | Description | Versie geïntroduceerd |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokaal IPv4-adres van de VM | 2017-04-02
| `ipv4.publicIpAddress` | Openbaar IPv4-adres van de VM | 2017-04-02
| `subnet.address` | Subnetadres van de VM | 2017-04-02
| `subnet.prefix` | Subnet-voorvoegsel, voorbeeld 24 | 2017-04-02
| `ipv6.ipAddress` | Lokaal IPv6-adres van de VM | 2017-04-02
| `macAddress` | Mac-adres van VM | 2017-04-02

### <a name="get-user-data"></a>Gebruikersgegevens op halen

Wanneer u een nieuwe VM maakt, kunt u een set gegevens opgeven die moet worden gebruikt tijdens of na de VM-inrichting en deze ophalen via IMDS. Als u gebruikersgegevens wilt instellen, [](https://aka.ms/ImdsUserDataArmTemplate)gebruikt u hier de quickstart-sjabloon . In het onderstaande voorbeeld ziet u hoe u deze gegevens kunt ophalen via IMDS.

> [!NOTE]
> Deze functie wordt uitgebracht met versie en is afhankelijk van een update voor het Azure-platform, dat momenteel wordt uitgerold en mogelijk nog `2021-01-01` niet in elke regio beschikbaar is.

> [!NOTE]
> Beveiligingsbericht: IMDS is geopend voor alle toepassingen op de VM. Gevoelige gegevens mogen niet in de gebruikersgegevens worden geplaatst.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Voorbeeld 1: VM bijhouden die wordt uitgevoerd in Azure

Als serviceprovider moet u mogelijk het aantal VM's met uw software bijhouden of agents hebben die de uniekheid van de VM moeten bijhouden. Als u een unieke id voor een VM wilt krijgen, gebruikt u het `vmId` veld uit Instance Metadata Service.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Voorbeeld 2: Plaatsing van verschillende gegevensreplica's

Voor bepaalde scenario's is de plaatsing van verschillende gegevensreplica's van groot belang. Voor plaatsing [van HDFS-replica's](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) of plaatsing van containers via een [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) moet u bijvoorbeeld weten wat de is en of de `platformFaultDomain` `platformUpdateDomain` VM wordt uitgevoerd.
U kunt ook [Beschikbaarheidszones](../articles/availability-zones/az-overview.md) instanties gebruiken om deze beslissingen te nemen.
U kunt rechtstreeks via IMDS query's uitvoeren op deze gegevens.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Voorbeeld 3: VM-tags op halen

VM-tags zijn opgenomen in de exemplaar-API onder het eindpunt instance/compute/tags.
Tags zijn mogelijk toegepast op uw Azure-VM om deze logisch te ordenen in een taxonomie. De tags die aan een VM zijn toegewezen, kunnen worden opgehaald met behulp van de onderstaande aanvraag.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

Het `tags` veld is een tekenreeks met de tags die worden scheidingstekens door puntkomma's. Deze uitvoer kan een probleem zijn als puntkomma's worden gebruikt in de tags zelf. Als een parser is geschreven om de tags programmatisch te extraheren, moet u vertrouwen op het `tagsList` veld . Het `tagsList` veld is een JSON-matrix zonder scheidingstekens en is daardoor gemakkelijker te parseren. De tagsList die is toegewezen aan een VM, kan worden opgehaald met behulp van de onderstaande aanvraag.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Voorbeeld 4: Meer informatie over de VM krijgen tijdens ondersteuningscase

Als serviceprovider kunt u een ondersteuningsgesprek ontvangen waarbij u meer informatie over de VM wilt. Als de klant wordt gevraagd om de metagegevens van de rekengegevens te delen, kan de ondersteuningsprofessional basisinformatie bieden over het soort VM in Azure.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Het antwoord is een JSON-tekenreeks. Het volgende voorbeeld van een antwoord wordt vrij afgedrukt voor de leesbaarheid.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Voorbeeld 5: De Azure-omgeving op halen waarin de VM wordt uitgevoerd

Azure heeft verschillende onafhankelijke clouds, [zoals Azure Government.](https://azure.microsoft.com/overview/clouds/government/) Soms hebt u de Azure-omgeving nodig om een aantal runtime-beslissingen te nemen. In het volgende voorbeeld ziet u hoe u dit gedrag kunt bereiken.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

De cloud en de waarden van de Azure-omgeving worden hier vermeld.

| Cloud | Azure-omgeving |
|-------|-------------------|
| [Alle algemeen beschikbare globale Azure-regio's](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Voorbeeld 6: Netwerkgegevens ophalen

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Voorbeeld 7: Openbaar IP-adres ophalen

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Attested-gegevens

### <a name="get-attested-data"></a>Attested-gegevens op halen

IMDS biedt garanties dat de verstrekte gegevens afkomstig zijn van Azure. Microsoft ondertekent een deel van deze informatie, zodat u kunt bevestigen dat een afbeelding in Azure Marketplace de afbeelding is die u in Azure gebruikt.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parameters

| Name | Vereist/optioneel | Beschrijving |
|------|-------------------|-------------|
| `api-version` | Vereist | De versie die wordt gebruikt om de aanvraag te verwerken.
| `nonce` | Optioneel | Een tekenreeks van 10 cijfers die als een cryptografische nonce fungeert. Als er geen waarde is opgegeven, gebruikt IMDS het huidige UTC-tijdstempel.

#### <a name="response"></a>Antwoord

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Vanwege het cachemechanisme van IMDS kan een eerder in de cache opgeslagen nonce-waarde worden geretourneerd.

De handtekeningblob is een [pkcs7-ondertekende](https://aka.ms/pkcs7)versie van het document. Het bevat het certificaat dat wordt gebruikt voor ondertekening, samen met bepaalde VM-specifieke details.

Voor VM's die zijn gemaakt met behulp van Azure Resource Manager, bevat het document , , , voor het maken en verlopen van het document, en de `vmId` `sku` `nonce` `subscriptionId` `timeStamp` planinformatie over de afbeelding. De plangegevens worden alleen ingevuld voor Azure Marketplace afbeeldingen.

Voor VM's die zijn gemaakt met behulp van het klassieke implementatiemodel, wordt alleen `vmId` gegarandeerd ingevuld. U kunt het certificaat uit het antwoord extraheren en dit gebruiken om te bevestigen dat het antwoord geldig is en afkomstig is uit Azure.

Het gedecodeerde document bevat de volgende velden:

| Gegevens | Description | Versie geïntroduceerd |
|------|-------------|--------------------|
| `licenseType` | Type licentie voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Dit is alleen aanwezig voor VM's met AHB-functie. | 2020-09-01
| `nonce` | Een tekenreeks die eventueel bij de aanvraag kan worden geleverd. Als er `nonce` geen is opgegeven, wordt de Coordinated Universal Time tijdstempel gebruikt. | 2018-10-01
| `plan` | Het [Azure Marketplace image-abonnement](/rest/api/compute/virtualmachines/createorupdate#plan). Bevat de abonnements-id (naam), de productafbeelding of aanbieding (product) en de uitgever-id (uitgever). | 2018-10-01
| `timestamp.createdOn` | Het UTC-tijdstempel voor wanneer het ondertekende document is gemaakt | 2018-20-01
| `timestamp.expiresOn` | Het UTC-tijdstempel voor wanneer het ondertekende document verloopt | 2018-10-01
| `vmId` | [Unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de VM | 2018-10-01
| `subscriptionId` | Azure-abonnement voor de virtuele machine | 2019-04-30
| `sku` | Specifieke SKU voor de VM-afbeelding | 2019-11-01

> [!NOTE]
> Voor klassieke (niet-Azure Resource Manager) VM's wordt gegarandeerd alleen de vmId ingevuld.

Voorbeelddocument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Voorbeeld 1: Valideren dat de VM wordt uitgevoerd in Azure

Leveranciers in Azure Marketplace ervoor zorgen dat hun software een licentie heeft om alleen in Azure te worden uitgevoerd. Als iemand de VHD naar een on-premises omgeving kopieert, moet de leverancier dat kunnen detecteren. Via IMDS kunnen deze leveranciers ondertekende gegevens krijgen die alleen reactie van Azure garanderen.

> [!NOTE]
> Voor dit voorbeeld moet het hulpprogramma jq worden geïnstalleerd.

**Validatie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Controleer of de handtekening afkomstig is Microsoft Azure controleer de certificaatketen op fouten.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Results**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Controleer of de handtekening van Microsoft Azure is en controleer de certificaatketen op fouten.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Vanwege het cachemechanisme van IMDS kan een eerder in de cache opgeslagen `nonce` waarde worden geretourneerd.

De `nonce` in het ondertekende document kan worden vergeleken als u een `nonce` parameter hebt opgegeven in de eerste aanvraag.

> [!NOTE]
> Het certificaat voor de openbare cloud en elke onafhankelijke cloud is anders.

| Cloud | Certificaat |
|-------|-------------|
| [Alle algemeen beschikbare algemene Azure-regio's](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> De certificaten komen mogelijk niet exact overeen met `metadata.azure.com` voor de openbare cloud. Daarom moet de certificeringsvalidatie een algemene naam van elk `.metadata.azure.com` subdomein toestaan.

In gevallen waarin het tussencertificaat niet kan worden gedownload vanwege netwerkbeperkingen tijdens de validatie, kunt u het tussencertificaat vastmaken. Azure rolt de certificaten over. Dit is de standaard PKI-praktijk. U moet de vastgemaakte certificaten bijwerken wanneer er een rollover wordt geïnstalleerd. Wanneer er een wijziging wordt gepland om het tussencertificaat bij te werken, wordt de Azure-blog bijgewerkt en worden Azure-klanten hiervan op de hoogte gesteld. 

U vindt de tussenliggende certificaten in de [PKI-opslagplaats](https://www.microsoft.com/pki/mscorp/cps/default.htm). De tussenliggende certificaten voor elk van de regio's kunnen verschillen.

> [!NOTE]
> Het tussencertificaat voor Azure China 21Vianet is van de DigiCert Global Root CA in plaats van Baltimore.
Als u de tussenliggende certificaten voor Azure China hebt vastgemaakt als onderdeel van een wijziging in de basisketen, moeten de tussenliggende certificaten worden bijgewerkt.


## <a name="managed-identity"></a>Beheerde identiteit

Een beheerde identiteit, toegewezen door het systeem, kan worden ingeschakeld op de VM. U kunt ook een of meer door de gebruiker toegewezen beheerde identiteiten toewijzen aan de VM.
Vervolgens kunt u tokens voor beheerde identiteiten aanvragen bij IMDS. Gebruik deze tokens om te verifiëren bij andere Azure-services, zoals Azure Key Vault.

Zie Een toegangs token verkrijgen voor gedetailleerde stappen voor het [inschakelen van deze functie.](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

## <a name="load-balancer-metadata"></a>Load Balancer metagegevens
Wanneer u instanties van virtuele machines of virtuele-machinesets achter een Azure Standard Load Balancer zet, kunt u IMDS gebruiken om metagegevens op te halen die betrekking hebben op de load balancer en de exemplaren. Zie Informatie over load balancer [ophalen voor meer informatie.](../articles/load-balancer/instance-metadata-service-load-balancer.md)

## <a name="scheduled-events"></a>Geplande gebeurtenissen
U kunt de status van de geplande gebeurtenissen verkrijgen met behulp van IMDS. Vervolgens kan de gebruiker een set acties opgeven die op deze gebeurtenissen moeten worden uitgevoerd. Zie Geplande gebeurtenissen voor Linux of [Geplande](../articles/virtual-machines/linux/scheduled-events.md) gebeurtenissen voor [Windows voor meer informatie.](../articles/virtual-machines/windows/scheduled-events.md)


## <a name="sample-code-in-different-languages"></a>Voorbeeldcode in verschillende talen

De volgende tabel bevat voorbeelden van het aanroepen van IMDS met behulp van verschillende talen in de VM:

| Taal | Voorbeeld: |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Aan de slag | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| Node.js | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Fouten en foutopsporing

Als er een gegevenselement niet is gevonden of een onjuist vormgeformeerde aanvraag, retourneert Instance Metadata Service standaard-HTTP-fouten. Bijvoorbeeld:

| HTTP-statuscode | Reden |
|------------------|--------|
| `200 OK` | De aanvraag is geslaagd.
| `400 Bad Request` | Ontbrekende `Metadata: true` header of ontbrekende parameter `format=json` bij het uitvoeren van een query op een leaf-knooppunt
| `404 Not Found` | Het aangevraagde element bestaat niet
| `405 Method Not Allowed` | De HTTP-methode (werkwoord) wordt niet ondersteund op het eindpunt.
| `410 Gone` | Het na enige tijd opnieuw proberen, maximaal 70 seconden
| `429 Too Many Requests` | Limieten [voor API-snelheid](#rate-limiting) zijn overschreden
| `500 Service Error` | Opnieuw proberen na enige tijd

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

- Ik krijg de fout `400 Bad Request, Required metadata header not specified` . Wat betekent dit?
  - IMDS vereist dat de header `Metadata: true` wordt doorgegeven in de aanvraag. Door deze header door te geven in de REST-aanroep is toegang tot IMDS mogelijk.

- Waarom krijg ik geen rekengegevens voor mijn VM?
  - Momenteel ondersteunt IMDS alleen instanties die zijn gemaakt met Azure Resource Manager.

- Ik heb mijn virtuele Azure Resource Manager enige tijd geleden gemaakt. Waarom zie ik geen informatie over rekenmetagegevens?
  - Als u uw VM na september 2016 hebt gemaakt, voegt u een [tag toe om](../articles/azure-resource-manager/management/tag-resources.md) rekenmetagegevens te bekijken. Als u uw VM vóór september 2016 hebt gemaakt, voegt u extensies of gegevensschijven toe aan het VM-exemplaar of verwijdert u deze om metagegevens te vernieuwen.

- Zijn gebruikersgegevens hetzelfde als aangepaste gegevens?
  - Gebruikersgegevens bieden dezelfde functionaliteit als aangepaste gegevens, zodat u uw eigen metagegevens kunt doorgeven aan het VM-exemplaar. Het verschil is dat gebruikersgegevens worden opgehaald via IMDS en persistent zijn gedurende de levensduur van het VM-exemplaar. De bestaande functie voor aangepaste gegevens blijft werken zoals beschreven in [dit artikel.](https://docs.microsoft.com/azure/virtual-machines/custom-data) U kunt echter alleen aangepaste gegevens krijgen via de lokale systeemmap, niet via IMDS.

- Waarom worden niet alle gegevens ingevuld voor een nieuwe versie?
  - Als u uw VM na september 2016 hebt gemaakt, voegt u een [tag toe om](../articles/azure-resource-manager/management/tag-resources.md) rekenmetagegevens te bekijken. Als u uw VM vóór september 2016 hebt gemaakt, voegt u extensies of gegevensschijven toe aan het VM-exemplaar of verwijdert u deze om metagegevens te vernieuwen.

- Waarom krijg ik de `500 Internal Server Error` foutmelding of `410 Resource Gone` ?
  - Uw aanvraag opnieuw proberen. Zie [Transient fault handling (Afhandeling van tijdelijke fouten) voor meer informatie.](/azure/architecture/best-practices/transient-faults) Als het probleem zich blijft voordoen, maakt u een ondersteuningsprobleem in de Azure Portal voor de VM.

- Zou dit werken voor exemplaren van virtuele-machineschaalsets?
  - Ja, IMDS is beschikbaar voor exemplaren van virtuele-machineschaalsets.

- Ik heb mijn tags bijgewerkt in virtuele-machineschaalsets, maar deze worden niet weergegeven in de exemplaren (in tegenstelling tot virtuele machines met één exemplaar). Doe ik iets verkeerd?
  - Op dit moment worden tags voor virtuele-machineschaalsets alleen aan de virtuele machine toegevoegd bij het opnieuw opstarten, opnieuw importeren of wijzigen van de schijf in het exemplaar.

- Waarom zie ik de SKU-informatie voor mijn VM niet in `instance/compute` detail?
  - Voor aangepaste afbeeldingen die zijn gemaakt Azure Marketplace, bewaart het Azure-platform niet de SKU-informatie voor de aangepaste afbeelding en de details voor VM's die zijn gemaakt op basis van de aangepaste afbeelding. Dit is een ontwerp en wordt daarom niet in de details van de VM aan het werk `instance/compute` laten komen.

- Waarom is er een time-out voor mijn aanvraag voor mijn aanroep naar de service?
  - Aanroepen van metagegevens moeten worden uitgevoerd vanaf het primaire IP-adres dat is toegewezen aan de primaire netwerkkaart van de VM. Als u uw routes hebt gewijzigd, moet er bovendien een route zijn voor het adres 169.254.169.254/32 in de lokale routeringstabel van uw VM.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Dump uw lokale routeringstabel en zoek naar de IMDS-vermelding. Bijvoorbeeld:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Controleer of er een route bestaat voor `169.254.169.254` en noteer de bijbehorende netwerkinterface (bijvoorbeeld `172.16.69.7` ).
    1. Dump de interfaceconfiguratie en zoek de interface die overeenkomt met de interface waarnaar wordt verwezen in de routeringstabel, met de notering van het MAC-adres (fysiek).
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Controleer of de interface overeenkomt met de primaire NIC en het primaire IP-adres van de VM. U kunt de primaire NIC en het IP-adres vinden door te kijken naar de netwerkconfiguratie in de Azure Portal of door deze op te zoeken met de Azure CLI. Noteer de privé-IP-adressen (en het MAC-adres als u de CLI gebruikt). Hier is een PowerShell CLI-voorbeeld:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Als ze niet overeenkomen, moet u de routeringstabel bijwerken zodat de primaire NIC en het IP-adres zijn gericht.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Dump uw lokale routeringstabel met een opdracht zoals `netstat -r` en zoek naar de IMDS-vermelding (bijvoorbeeld):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Controleer of er een route bestaat voor `169.254.169.254` en noteer de bijbehorende netwerkinterface (bijvoorbeeld `eth0` ).
    1. Dump de interfaceconfiguratie voor de bijbehorende interface in de routeringstabel (de exacte naam van het configuratiebestand kan variëren)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Als u een dynamisch IP-adres gebruikt, noteer dan het MAC-adres. Als u een statisch IP-adres gebruikt, ziet u mogelijk de vermelde IP('s) en/of het MAC-adres.
    1. Controleer of de interface overeenkomt met de primaire NIC en het primaire IP-adres van de VM. U kunt de primaire NIC en het IP-adres vinden door te kijken naar de netwerkconfiguratie in de Azure Portal of door deze op te zoeken met de Azure CLI. Noteer de privé-IP-adressen (en het MAC-adres als u de CLI gebruikt). Hier is een PowerShell CLI-voorbeeld:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Als ze niet overeenkomen, moet u de routeringstabel bijwerken zodat de primaire NIC/IP wordt gebruikt.

    ---

- Failoverclustering in Windows Server
  - Wanneer u een query uitvoert op IMDS met failoverclustering, is het soms nodig om een route toe te voegen aan de routeringstabel. U doet dit als volgt:

    1. Open een opdrachtprompt met beheerdersbevoegdheden.

    1. Voer de volgende opdracht uit en noteer het adres van de Interface voor netwerkbestemming ( `0.0.0.0` ) in de IPv4-routetabel.

    ```bat
    route print
    ```

    > [!NOTE]
    > De volgende voorbeelduitvoer is afkomstig van een Windows Server-VM met failovercluster ingeschakeld. Ter vereenvoudiging bevat de uitvoer alleen de IPv4-routetabel.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Voer de volgende opdracht uit en gebruik het adres van de Interface voor netwerkbestemming ( `0.0.0.0` ), dat in dit voorbeeld is ( `10.0.1.10` ).

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Ondersteuning

Als u na meerdere pogingen geen antwoord op metagegevens kunt krijgen, kunt u een ondersteuningsprobleem maken in de Azure Portal.

## <a name="product-feedback"></a>Productfeedback

U kunt hier productfeedback en ideeën aan ons feedbackkanaal voor gebruikers Virtual Machines > Instance Metadata Service [geven](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs token verkrijgen voor de VM](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Geplande gebeurtenissen voor Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Geplande gebeurtenissen voor Windows](../articles/virtual-machines/windows/scheduled-events.md)
