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
ms.openlocfilehash: 0b04ebd9672990738d77bc5ae09d7f7fae4ffb9d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500197"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)

De Azure Instance Metadata Service (IMDS) bevat informatie over actieve exemplaren van virtuele machines. U kunt deze gebruiken om uw virtuele machines te beheren en te configureren.
Deze informatie omvat de gebeurtenissen SKU, opslag, netwerk configuraties en gepland onderhoud. Zie overzicht van de [eindpunt categorieën](#endpoint-categories)voor een volledige lijst met beschik bare gegevens.

IMDS is beschikbaar voor het uitvoeren van exemplaren van virtuele machines (Vm's) en instanties van virtuele-machine schaal sets. Alle eind punten ondersteunen Vm's die zijn gemaakt en beheerd met behulp van [Azure Resource Manager](/rest/api/resources/). Alleen de attesten categorie en het netwerk gedeelte van de categorie exemplaar ondersteunen Vm's die zijn gemaakt met behulp van het klassieke implementatie model. Het attested-eind punt heeft daarom slechts een beperkt gebied.

IMDS is een REST API die beschikbaar is met een bekend, niet-routeerbaar IP-adres ( `169.254.169.254` ). U kunt de app alleen openen vanuit de VM. De communicatie tussen de virtuele machine en de IMDS verlaat nooit de host.
Laat uw HTTP-clients Web-proxy's in de virtuele machine omzeilen tijdens het uitvoeren van een query op IMDS, en behandel `169.254.169.254` hetzelfde als [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Gebruik

### <a name="access-azure-instance-metadata-service"></a>Toegang tot Azure Instance Metadata Service

Om toegang te krijgen tot IMDS, maakt u een virtuele machine op basis van [Azure Resource Manager](/rest/api/resources/) of de [Azure Portal](https://portal.azure.com)en gebruikt u de volgende voor beelden.
Zie voor meer voor beelden [Azure instance meta data samples](https://github.com/microsoft/azureimds).

Hier volgt een voorbeeld code voor het ophalen van alle meta gegevens voor een exemplaar. Zie [endpoint-categorieën](#endpoint-categories) voor een overzicht van alle beschik bare functies voor toegang tot een specifieke gegevens bron.

**Aanvraag**

> [!IMPORTANT]
> In dit voor beeld worden proxy's omzeild. U **moet** proxy's overs Laan bij het uitvoeren van een query op IMDS. Zie [proxy's](#proxies) voor aanvullende informatie.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Verificatie en beveiliging

De Instance Metadata Service is alleen toegankelijk vanuit een actief exemplaar van een virtuele machine op een niet-routeerbaar IP-adres. Vm's zijn beperkt tot interactie met meta gegevens/functionaliteit die op zichzelf betrekking hebben. De API is alleen HTTP en verlaat nooit de host.

Om ervoor te zorgen dat aanvragen direct bedoeld zijn voor IMDS en onbedoelde of ongewenste omleiding van aanvragen voor komen:
- **Moet** de koptekst bevatten `Metadata: true`
- Mag **geen** koptekst bevatten `X-Forwarded-For`

Alle aanvragen die **niet voldoen aan** deze vereisten, worden geweigerd door de service.

> [!IMPORTANT]
> IMDS is **geen** kanaal voor gevoelige gegevens. De API is niet-geverifieerd en open voor alle processen op de virtuele machine. Informatie die via deze service wordt weer gegeven, moet worden beschouwd als gedeelde gegevens voor alle toepassingen die in de virtuele machine worden uitgevoerd.

## <a name="proxies"></a>Proxy's

IMDS is **niet** bedoeld om te worden gebruikt achter een proxy en dit wordt niet ondersteund. De meeste HTTP-clients bieden een optie waarmee u proxy's op uw aanvragen kunt uitschakelen en deze functionaliteit moet worden gebruikt bij de communicatie met IMDS. Raadpleeg de documentatie van uw client voor meer informatie.

> [!IMPORTANT]
> Zelfs als u geen proxy configuratie kent in uw omgeving, **moet u de standaard instellingen voor de client proxy negeren**. Proxy configuraties kunnen automatisch worden gedetecteerd en niet worden omzeild, waardoor dergelijke configuraties worden weer gegeven om te voor komen dat er storingen optreden, moet de configuratie van de computer in de toekomst worden gewijzigd.

## <a name="rate-limiting"></a>Snelheidsbeperking

Over het algemeen zijn aanvragen voor IMDS beperkt tot 5 aanvragen per seconde. Aanvragen die de drempel waarde overschrijden, worden met 429 reacties afgewezen. Aanvragen voor de categorie [beheerde identiteit](#managed-identity) zijn beperkt tot 20 aanvragen per seconde en vijf gelijktijdige aanvragen.

## <a name="http-verbs"></a>HTTP-woorden

De volgende HTTP-termen worden momenteel ondersteund:

| Verb | Beschrijving |
|------|-------------|
| `GET` | De aangevraagde resource ophalen

## <a name="parameters"></a>Parameters

Eind punten ondersteunen mogelijk vereiste en/of optionele para meters. Zie [schema](#schema) en de documentatie voor het desbetreffende eind punt voor meer informatie.

### <a name="query-parameters"></a>Queryparameters

IMDS-eind punten bieden ondersteuning voor HTTP-query teken reeks parameters. Bijvoorbeeld: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Hiermee geeft u de para meters op:

| Name | Waarde |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Aanvragen met dubbele query parameter namen worden geweigerd.

### <a name="route-parameters"></a>Route parameters

Voor sommige eind punten die grotere JSON-blobs retour neren, ondersteunen we het toevoegen van route parameters aan het eind punt van de aanvraag om te filteren op een subset van het antwoord: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
De para meters komen overeen met de Indexen/sleutels die worden gebruikt om het JSON-object uit te voeren, maar u communiceert met een geparseerde weer gave.

`/metatadata/instance`Retourneert bijvoorbeeld het JSON-object:
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

Als we het antwoord op alleen de reken eigenschap willen filteren, verzenden we de aanvraag: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

En als we willen filteren op een geneste eigenschap of specifiek matrix element, worden sleutels toegevoegd: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtert op het eerste element van de `Network.interface` eigenschap en retourneert het volgende:

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
> Wanneer u filtert op een Leaf-knoop punt, `format=json` werkt niet. Voor deze query's `format=text` moet expliciet worden opgegeven, aangezien de standaard indeling JSON is.

## <a name="schema"></a>Schema

### <a name="data-format"></a>Gegevensindeling

IMDS retourneert standaard gegevens in JSON-indeling ( `Content-Type: application/json` ). Eind punten die ondersteuning bieden voor het filteren van antwoorden (Zie [route parameters](#route-parameters)) ondersteunen ook de indeling `text` .

Om toegang te krijgen tot een niet-standaard antwoord indeling, geeft u de aangevraagde indeling op als een query reeks parameter in de aanvraag. Bijvoorbeeld:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

In JSON-antwoorden worden alle primitieven van het type `string` en ontbreken ontbrekende of niet-toepasselijke waarden altijd, maar worden ze ingesteld op een lege teken reeks.

### <a name="versioning"></a>Versiebeheer

IMDS is Versioning en het opgeven van de API-versie in de HTTP-aanvraag is verplicht. De enige uitzonde ring op deze vereiste is het eind punt van de [versie](#versions) , dat kan worden gebruikt om de beschik bare API-versies dynamisch op te halen.

Als nieuwere versies worden toegevoegd, kunnen oudere versies nog steeds worden gebruikt voor compatibiliteit als uw scripts afhankelijk zijn van specifieke gegevens indelingen.

Wanneer u geen versie opgeeft, krijgt u een fout melding met een lijst met de nieuwste ondersteunde versies:
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

> [!NOTE]
> Versie 2020-10-01 wordt momenteel geïmplementeerd en is mogelijk nog niet beschikbaar in elke regio.

### <a name="swagger"></a>Swagger

Een volledige Swagger-definitie voor IMDS is beschikbaar op: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regionale beschikbaarheid

De service is **algemeen beschikbaar** in alle Azure-Clouds.

## <a name="root-endpoint"></a>Hoofd eindpunt

Het hoofd eindpunt is `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Eindpunt Categorieën

De IMDS-API bevat meerdere eindpunt categorieën die verschillende gegevens bronnen vertegenwoordigen, waarvan elk een of meer eind punten bevat. Zie elke categorie voor meer informatie.

| Hoofdmap van categorie | Beschrijving | Geïntroduceerde versie |
|---------------|-------------|--------------------|
| `/metadata/attested` | Zie [attested data](#attested-data) | 2018-10-01
| `/metadata/identity` | Zie [beheerde identiteit via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Zie [meta gegevens van instantie](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | Zie [Scheduled Events via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | [Versies](#versions) weer geven | N.v.t.

## <a name="versions"></a>Versies

> [!NOTE]
> Deze functie is uitgebracht naast versie 2020-10-01, die momenteel wordt geïmplementeerd en nog niet beschikbaar is in elke regio.

### <a name="list-api-versions"></a>API-versies weer geven

Retourneert de set ondersteunde API-versies.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parameters

Geen (dit eind punt is niet-versie).

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

## <a name="instance-metadata"></a>Meta gegevens van instantie

### <a name="get-vm-metadata"></a>VM-meta gegevens ophalen

Beschrijft de belang rijke meta gegevens voor de VM-instantie, met inbegrip van compute, Network en storage. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parameters

| Name | Vereist/optioneel | Beschrijving |
|------|-------------------|-------------|
| `api-version` | Vereist | De versie die wordt gebruikt om de aanvraag te onderhouden.
| `format` | Beschrijving | De indeling ( `json` of `text` ) van het antwoord. * Opmerking: is mogelijk vereist bij het gebruik van aanvraag parameters

Dit eind punt ondersteunt antwoord filtering via [route parameters](#route-parameters).

#### <a name="response"></a>Antwoord

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Schema-uitsplitsing:

**Compute**

| Gegevens | Beschrijving | Geïntroduceerde versie |
|------|-------------|--------------------|
| `azEnvironment` | Azure-omgeving waarin de virtuele machine wordt uitgevoerd | 2018-10-01
| `customData` | Deze functie is momenteel uitgeschakeld. Deze documentatie wordt bijgewerkt wanneer deze beschikbaar wordt | 2019-02-01
| `isHostCompatibilityLayerVm` | Hiermee wordt aangegeven of de virtuele machine wordt uitgevoerd op de compatibiliteit slaag van de host | 2020-06-01
| `licenseType` | Het type licentie voor de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Dit is alleen aanwezig voor Vm's met AHB-functionaliteit | 2020-09-01
| `location` | Azure-regio waarin de virtuele machine wordt uitgevoerd | 2017-04-02
| `name` | Naam van de virtuele machine | 2017-04-02
| `offer` | Informatie over de installatie kopie van de virtuele machine weer geven en die alleen aanwezig is voor installatie kopieën die vanuit de Azure-installatie kopie galerie | 2017-04-02
| `osProfile.adminUsername` | Hiermee geeft u de naam van het beheerders account op | 2020-07-15
| `osProfile.computerName` | Hiermee geeft u de naam van de computer | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Hiermee wordt aangegeven of wachtwoord verificatie is uitgeschakeld. Dit is alleen aanwezig voor virtuele Linux-machines | 2020-10-01
| `osType` | Linux of Windows | 2017-04-02
| `placementGroupId` | [Plaatsings groep](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) van de schaalset voor virtuele machines | 2017-08-01
| `plan` | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) met naam, product en uitgever voor een virtuele machine als dit een Azure Marketplace-installatie kopie is | 2018-04-02
| `platformUpdateDomain` |  [Domein bijwerken](../articles/virtual-machines/manage-availability.md) waarop de VM wordt uitgevoerd | 2017-04-02
| `platformFaultDomain` | [Fout domein](../articles/virtual-machines/manage-availability.md) waarop de VM wordt uitgevoerd | 2017-04-02
| `provider` | Provider van de virtuele machine | 2018-10-01
| `publicKeys` | [Verzameling van open bare sleutels](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) die zijn toegewezen aan de virtuele machine en de paden | 2018-04-02
| `publisher` | Uitgever van de VM-installatie kopie | 2017-04-02
| `resourceGroupName` | [Resource groep](../articles/azure-resource-manager/management/overview.md) voor uw virtuele machine | 2017-08-01
| `resourceId` | De [volledig gekwalificeerde](/rest/api/resources/resources/getbyid) id van de resource | 2019-03-11
| `sku` | Specifieke SKU voor de VM-installatie kopie | 2017-04-02
| `securityProfile.secureBootEnabled` | Hiermee wordt aangegeven of UEFI Secure boot is ingeschakeld op de VM | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Hiermee wordt aangegeven of de virtuele Trusted Platform Module (TPM) is ingeschakeld op de VM | 2020-06-01
| `storageProfile` | Zie het onderstaande opslag profiel | 2019-06-01
| `subscriptionId` | Azure-abonnement voor de virtuele machine | 2017-08-01
| `tags` | [Labels](../articles/azure-resource-manager/management/tag-resources.md) voor uw virtuele machine  | 2017-08-01
| `tagsList` | Tags die zijn opgemaakt als een JSON-matrix voor eenvoudiger programmatisch parseren  | 2019-06-04
| `version` | Versie van de VM-installatie kopie | 2017-04-02
| `vmId` | De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine | 2017-04-02
| `vmScaleSetName` | [Naam van de schaalset voor virtuele machines](../articles/virtual-machine-scale-sets/overview.md) van de schaalset voor virtuele machines | 2017-12-01
| `vmSize` | [VM-grootte](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Beschikbaarheids zone](../articles/availability-zones/az-overview.md) van uw virtuele machine | 2017-12-01

**Opslagprofiel**

Het opslag profiel van een virtuele machine is onderverdeeld in drie categorieën: afbeeldings verwijzing, besturingssysteem schijf en gegevens schijven.

Het verwijzings object voor de afbeelding bevat de volgende informatie over de installatie kopie van het besturings systeem:

| Gegevens | Beschrijving |
|------|-------------|
| `id` | Resource-id
| `offer` | Aanbieding van de installatie kopie van het platform of de Marketplace
| `publisher` | Uitgever van installatie kopie
| `sku` | Afbeeldings-SKU
| `version` | Versie van de installatie kopie van het platform of de Marketplace

Het object van de besturingssysteem schijf bevat de volgende informatie over de besturingssysteem schijf die wordt gebruikt door de virtuele machine:

| Gegevens | Beschrijving |
|------|-------------|
| `caching` | Cache vereisten
| `createOption` | Informatie over de manier waarop de virtuele machine is gemaakt
| `diffDiskSettings` | Tijdelijke schijf instellingen
| `diskSizeGB` | Grootte van de schijf in GB
| `image`   | Virtuele harde schijf voor installatie kopie van bron gebruiker
| `lun`     | Nummer van de logische eenheid van de schijf
| `managedDisk` | Beheerde schijf parameters
| `name`    | Schijf naam
| `vhd`     | Virtuele harde schijf
| `writeAcceleratorEnabled` | Hiermee wordt aangegeven of Write Accelerator is ingeschakeld op de schijf

De matrix gegevens schijven bevat een lijst met gegevens schijven die zijn gekoppeld aan de VM. Elk gegevens schijf object bevat de volgende informatie:

Gegevens | Beschrijving |
-----|-------------|
| `caching` | Cache vereisten
| `createOption` | Informatie over de manier waarop de virtuele machine is gemaakt
| `diffDiskSettings` | Tijdelijke schijf instellingen
| `diskSizeGB` | Grootte van de schijf in GB
| `encryptionSettings` | Versleutelings instellingen voor de schijf
| `image` | Virtuele harde schijf voor installatie kopie van bron gebruiker
| `managedDisk` | Beheerde schijf parameters
| `name` | Schijf naam
| `osType` | Type besturings systeem dat is opgenomen in de schijf
| `vhd` | Virtuele harde schijf
| `writeAcceleratorEnabled` | Hiermee wordt aangegeven of Write Accelerator is ingeschakeld op de schijf

**Netwerk**

| Gegevens | Beschrijving | Geïntroduceerde versie |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokaal IPv4-adres van de virtuele machine | 2017-04-02
| `ipv4.publicIpAddress` | Openbaar IPv4-adres van de virtuele machine | 2017-04-02
| `subnet.address` | Subnetadres van de virtuele machine | 2017-04-02
| `subnet.prefix` | Subnetvoorvoegsel, voor beeld 24 | 2017-04-02
| `ipv6.ipAddress` | Lokaal IPv6-adres van de virtuele machine | 2017-04-02
| `macAddress` | Mac-adres van VM | 2017-04-02

**VM-Tags**

VM-Tags zijn opgenomen in de exemplaar-API onder instance/Compute/Tags-eind punt.
Labels zijn mogelijk toegepast op uw virtuele Azure-machine om ze logisch in een taxonomie te organiseren. De labels die aan een virtuele machine zijn toegewezen, kunnen worden opgehaald met behulp van de onderstaande aanvraag.

Het `tags` veld is een teken reeks met de Tags gescheiden door punt komma's. Deze uitvoer kan een probleem zijn als punt komma's worden gebruikt in de labels zelf. Als een parser is geschreven om de Tags programmatisch uit te pakken, moet u vertrouwen op het `tagsList` veld. Het `tagsList` veld is een JSON-matrix met geen scheidings tekens, en kan daarom gemakkelijker worden geparseerd.


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Voor beeld 1: een virtuele machine volgen die wordt uitgevoerd op Azure

Als service provider kan het nodig zijn om het aantal Vm's waarop de software wordt uitgevoerd bij te houden of om agents te hebben die de uniekheid van de virtuele machine moeten bijhouden. Als u een unieke ID voor een virtuele machine wilt ophalen, gebruikt u het `vmId` veld van instance metadata service.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"| ConvertTo-Json
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

#### <a name="sample-2-placement-of-different-data-replicas"></a>Voor beeld 2: plaatsing van verschillende gegevens replica's

Voor bepaalde scenario's is het plaatsen van verschillende gegevens replica's van groot belang. Voor de plaatsing van [HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) of containers via een [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kan het nodig zijn dat u weet dat de `platformFaultDomain` en `platformUpdateDomain` de virtuele machine wordt uitgevoerd.
U kunt [Beschikbaarheidszones](../articles/availability-zones/az-overview.md) ook gebruiken voor de instanties om deze beslissingen te nemen.
U kunt deze gegevens rechtstreeks opvragen via IMDS.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" | ConvertTo-Json
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

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Voor beeld 3: meer informatie over de VM ophalen tijdens de ondersteunings Case

Als service provider kunt u een ondersteunings oproep krijgen waarin u meer informatie over de virtuele machine wilt weten. Als u de klant vraagt om de berekenings-meta gegevens te delen, kan de ondersteunings medewerker basis informatie geven over het type virtuele machine op Azure.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Het antwoord is een JSON-teken reeks. Het volgende voor beeld is een mooie afdruk van de Lees baarheid.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
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
    "sku": "Windows-Server-2012-R2-Datacenter",
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
            "osType": "Linux",
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

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Voor beeld 4: de Azure-omgeving waar de virtuele machine wordt uitgevoerd, ophalen

Azure heeft verschillende soevereine Clouds, zoals [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Soms hebt u de Azure-omgeving nodig om enkele runtime beslissingen te nemen. In het volgende voor beeld ziet u hoe u dit gedrag kunt behaalt.

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text" | ConvertTo-Json
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

De Cloud en de waarden van de Azure-omgeving worden hier weer gegeven.

| Cloud | Azure-omgeving |
|-------|-------------------|
| [Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-5-retrieve-network-information"></a>Voor beeld 5: netwerk gegevens ophalen

**Aanvraag**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json
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

#### <a name="sample-6-retrieve-public-ip-address"></a>Voor beeld 6: openbaar IP-adres ophalen

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Attestende gegevens

### <a name="get-attested-data"></a>Attestation-gegevens ophalen

IMDS helpt garanties te geven dat de verstrekte gegevens afkomstig zijn van Azure. Micro soft tekent een deel van deze informatie, zodat u kunt bevestigen dat een installatie kopie in azure Marketplace de versie is die u op Azure uitvoert.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parameters

| Name | Vereist/optioneel | Beschrijving |
|------|-------------------|-------------|
| `api-version` | Vereist | De versie die wordt gebruikt om de aanvraag te onderhouden.
| `nonce` | Optioneel | Een teken reeks van 10 cijfers die fungeert als een cryptografische nonce. Als er geen waarde wordt gegeven, gebruikt IMDS de huidige UTC-tijds tempel.

#### <a name="response"></a>Antwoord

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Vanwege het cache mechanisme van IMDS kan een eerder in de cache opgeslagen nonce-waarde worden geretourneerd.

De hand tekening-blob is een door [pkcs7](https://aka.ms/pkcs7)ondertekende versie van het document. Het bevat het certificaat dat wordt gebruikt voor het ondertekenen samen met bepaalde specifieke details van de virtuele machine.

Voor virtuele machines die zijn gemaakt met behulp van Azure Resource Manager, bevat het document,,,, `vmId` `sku` voor het `nonce` `subscriptionId` `timeStamp` maken en het verstrijken van het document en de plannings informatie over de installatie kopie. De plan gegevens worden alleen ingevuld voor installatie kopieën van Azure Marketplace.

Voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model, is alleen de `vmId` garantie dat deze is ingevuld. U kunt het certificaat extra heren uit het antwoord en dit gebruiken om te bevestigen dat het antwoord geldig is en afkomstig is van Azure.

Het gedecodeerde document bevat de volgende velden:

| Gegevens | Beschrijving | Geïntroduceerde versie |
|------|-------------|--------------------|
| `licenseType` | Het type licentie voor de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Dit is alleen aanwezig voor Vm's met AHB-functionaliteit. | 2020-09-01
| `nonce` | Een teken reeks die optioneel kan worden meegeleverd met de aanvraag. Als er geen `nonce` is opgegeven, wordt de huidige UTC (Coordinated Universal Time Time Stamp) gebruikt. | 2018-10-01
| `plan` | Het [abonnement op de Azure Marketplace-installatie kopie](/rest/api/compute/virtualmachines/createorupdate#plan). Bevat de plan-ID (naam), product afbeelding of aanbieding (product) en uitgever-ID (uitgever). | 2018-10-01
| `timestamp.createdOn` | De UTC-tijds tempel voor het maken van het ondertekende document | 2018-20-01
| `timestamp.expiresOn` | De UTC-tijds tempel voor het verloopt van het ondertekende document | 2018-10-01
| `vmId` | De [unieke id](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) voor de virtuele machine | 2018-10-01
| `subscriptionId` | Azure-abonnement voor de virtuele machine | 2019-04-30
| `sku` | Specifieke SKU voor de VM-installatie kopie | 2019-11-01

> [!NOTE]
> Voor klassieke (niet-Azure Resource Manager) Vm's wordt alleen de vmId gegarandeerd.

Voorbeeld document:
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

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Voor beeld 1: controleren of de virtuele machine wordt uitgevoerd in azure

Leveranciers in azure Marketplace willen ervoor zorgen dat hun software een licentie heeft om alleen in azure uit te voeren. Als iemand de VHD naar een on-premises omgeving kopieert, moet de leverancier dat kunnen detecteren. Via IMDS kunnen deze leveranciers ondertekende gegevens ophalen die alleen reageren op Azure.

> [!NOTE]
> Voor dit voor beeld moet het JQ-hulp programma worden geïnstalleerd.

**Validatie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Controleer of de hand tekening van Microsoft Azure is en controleer de certificaat keten op fouten.

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

Controleer of de hand tekening van Microsoft Azure is en controleer de certificaat keten op fouten.

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
> Vanwege het cache mechanisme van IMDS, kan een eerder in de cache opgeslagen `nonce` waarde worden geretourneerd.

De `nonce` in het ondertekende document kan worden vergeleken als u een `nonce` para meter hebt ingevoerd in de eerste aanvraag.

> [!NOTE]
> Het certificaat voor de open bare Cloud en elke soevereine Cloud wijken af.

| Cloud | Certificaat |
|-------|-------------|
| [Alle algemeen beschik bare wereld wijde Azure-regio's](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> De certificaten hebben mogelijk niet exact overeenkomen met `metadata.azure.com` de open bare Cloud. Daarom moet de certificerings validatie een algemene naam van een wille keurig `.metadata.azure.com` subdomein toestaan.

In gevallen waarin het tussenliggende certificaat niet kan worden gedownload vanwege netwerk beperkingen tijdens de validatie, kunt u het tussenliggende certificaat vastmaken. Azure rolt over de certificaten. Dit is standaard PKI-prak tijken. U moet de vastgemaakte certificaten bijwerken wanneer u overschakelt. Wanneer een wijziging voor het bijwerken van het tussenliggende certificaat is gepland, wordt het Azure-blog bijgewerkt en ontvangen Azure-klanten een melding. 

U kunt de tussenliggende certificaten vinden in de [PKI-opslag plaats](https://www.microsoft.com/pki/mscorp/cps/default.htm). De tussenliggende certificaten voor elk van de regio's kunnen verschillen.

> [!NOTE]
> Het tussenliggende certificaat voor Azure China 21Vianet is van DigiCert globale basis certificerings instantie, in plaats van Baltimore.
Als u de tussenliggende certificaten voor Azure China hebt vastgemaakt als onderdeel van een wijziging in de hoofd keten van de instantie, moeten de tussenliggende certificaten worden bijgewerkt.


## <a name="managed-identity"></a>Beheerde identiteit

Een beheerde identiteit, toegewezen door het systeem, kan worden ingeschakeld op de VM. U kunt ook een of meer door de gebruiker toegewezen beheerde identiteiten toewijzen aan de virtuele machine.
Vervolgens kunt u tokens aanvragen voor beheerde identiteiten vanuit IMDS. Gebruik deze tokens om te verifiëren bij andere Azure-Services, zoals Azure Key Vault.

Zie [een toegangs token verkrijgen](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)voor gedetailleerde stappen om deze functie in te scha kelen.

## <a name="scheduled-events"></a>Geplande gebeurtenissen
U kunt de status van de geplande gebeurtenissen verkrijgen met behulp van IMDS. Vervolgens kan de gebruiker een reeks acties opgeven die moeten worden uitgevoerd op deze gebeurtenissen. Zie [geplande gebeurtenissen voor Linux](../articles/virtual-machines/linux/scheduled-events.md) of [geplande gebeurtenissen voor Windows](../articles/virtual-machines/windows/scheduled-events.md)voor meer informatie.

## <a name="sample-code-in-different-languages"></a>Voorbeeld code in verschillende talen

De volgende tabel bevat voor beelden van het aanroepen van IMDS met behulp van verschillende talen in de virtuele machine:

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

## <a name="errors-and-debugging"></a>Fouten en fout opsporing

Als er geen gegevens element is gevonden of een ongeldige aanvraag is, retourneert de Instance Metadata Service standaard HTTP-fouten. Bijvoorbeeld:

| HTTP-statuscode | Reden |
|------------------|--------|
| `200 OK` | De aanvraag is voltooid.
| `400 Bad Request` | Ontbrekende `Metadata: true` koptekst of ontbrekende para meter `format=json` bij het uitvoeren van een query op een Leaf-knoop punt
| `404 Not Found` | Het aangevraagde element bestaat niet
| `405 Method Not Allowed` | De HTTP-methode (werk woord) wordt niet ondersteund op het eind punt.
| `410 Gone` | Probeer het na enige tijd opnieuw uit met een maximum van 70 seconden
| `429 Too Many Requests` | De API- [frequentie limieten](#rate-limiting) is overschreden
| `500 Service Error` | Na enige tijd opnieuw proberen

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Ik krijg de fout melding `400 Bad Request, Required metadata header not specified` . Wat betekent dit?**

IMDS vereist dat de header `Metadata: true` in de aanvraag wordt door gegeven. Als deze header door de REST-aanroep wordt door gegeven, is toegang tot IMDS mogelijk.

**Waarom krijg ik geen reken gegevens voor mijn virtuele machine?**

Momenteel ondersteunt IMDS alleen instanties die zijn gemaakt met Azure Resource Manager.

**Ik heb Azure Resource Manager enige tijd geleden mijn virtuele machine gemaakt. Waarom zie ik geen gegevens over de berekening van meta gegevens?**

Als u na september 2016 uw VM hebt gemaakt, voegt u een [tag](../articles/azure-resource-manager/management/tag-resources.md) toe om de reken-meta gegevens te bekijken. Als u vóór september 2016 uw VM hebt gemaakt, kunt u uitbrei dingen of gegevens schijven toevoegen aan of verwijderen uit het VM-exemplaar om meta gegevens te vernieuwen.

**Waarom worden niet alle gegevens weer gegeven die zijn ingevoerd voor een nieuwe versie?**

Als u na september 2016 uw VM hebt gemaakt, voegt u een [tag](../articles/azure-resource-manager/management/tag-resources.md) toe om de reken-meta gegevens te bekijken. Als u vóór september 2016 uw VM hebt gemaakt, kunt u uitbrei dingen of gegevens schijven toevoegen aan of verwijderen uit het VM-exemplaar om meta gegevens te vernieuwen.

**Waarom krijg ik de fout `500 Internal Server Error` of `410 Resource Gone` ?**

Probeer de aanvraag opnieuw uit te voeren. Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor meer informatie. Als het probleem zich blijft voordoen, maakt u een ondersteunings probleem in de Azure Portal voor de virtuele machine.

**Zou dit werken voor instanties van de schaalset voor virtuele machines?**

Ja, IMDS is beschikbaar voor instanties van virtuele-machine schaal sets.

**Ik heb mijn tags in virtuele-machine schaal sets bijgewerkt, maar ze worden niet weer gegeven in de instanties (in tegens telling tot Vm's met één exemplaar). Wil ik iets verkeerd doen?**

Momenteel worden labels voor virtuele-machine schaal sets alleen weer gegeven voor de VM op het moment dat de computer opnieuw wordt opgestart, de installatie kopie of de schijf wordt gewijzigd.

**Waarom is er een time-out opgetreden voor mijn aanvraag voor mijn oproep naar de service?**

Meta gegevens moeten worden gemaakt op basis van het primaire IP-adres dat is toegewezen aan de primaire netwerk kaart van de virtuele machine. Als u uw routes hebt gewijzigd, moet er bovendien een route voor het 169.254.169.254/32-adres in de lokale routerings tabel van de VM zijn.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Dump uw lokale routerings tabel en zoek naar de IMDS-vermelding. Bijvoorbeeld:
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
1. Controleer of er een route bestaat voor `169.254.169.254` en noteer de bijbehorende netwerk interface (bijvoorbeeld `172.16.69.7` ).
1. Dump de configuratie van de interface en zoek de interface die overeenkomt met het punt waarnaar wordt verwezen in de routerings tabel. Dit is het MAC-adres (fysiek).
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
1. Controleer of de interface overeenkomt met de primaire NIC van de virtuele machine en het primaire IP-adres. U kunt de primaire NIC en het IP-adres vinden door te kijken naar de netwerk configuratie in de Azure Portal of door de Azure CLI te bekijken. Noteer de privé Ip's (en het MAC-adres als u de CLI gebruikt). Hier volgt een Power shell CLI-voor beeld:
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
1. Als ze niet overeenkomen, werkt u de routerings tabel bij zodat de primaire NIC en IP zijn gericht.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Dump uw lokale routerings tabel met een opdracht zoals `netstat -r` en zoek naar het IMDS-item (bijvoorbeeld):
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Controleer of er een route bestaat voor `169.254.169.254` en noteer de bijbehorende netwerk interface (bijvoorbeeld `eth0` ).
1. De interface configuratie voor de bijbehorende interface in de routerings tabel dumpen (Let op de exacte naam van het configuratie bestand kan variëren)
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
1. Als u een dynamisch IP-adres gebruikt, noteert u de MAC-adressen. Als u een statisch IP-adres gebruikt, noteert u mogelijk de vermelde IP ('s) en/of MAC-adressen.
1. Controleer of de interface overeenkomt met de primaire NIC van de virtuele machine en het primaire IP-adres. U kunt de primaire NIC en het IP-adres vinden door te kijken naar de netwerk configuratie in de Azure Portal of door de Azure CLI te bekijken. Noteer de privé Ip's (en het MAC-adres als u de CLI gebruikt). Hier volgt een Power shell CLI-voor beeld:
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
1. Als ze niet overeenkomen, werkt u de routerings tabel bij, zodat de primaire NIC/IP is gericht.

---

**Failover Clustering in Windows Server**

Wanneer u een query uitvoert op IMDS met failover clustering, is het soms nodig om een route toe te voegen aan de routerings tabel. U doet dit als volgt:

1. Open een opdrachtprompt met beheerdersbevoegdheden.

1. Voer de volgende opdracht uit en noteer het adres van de interface voor de netwerk bestemming ( `0.0.0.0` ) in de IPv4-route tabel.

```bat
route print
```

> [!NOTE]
> De volgende voorbeeld uitvoer is van een Windows Server-VM met een failovercluster ingeschakeld. Voor de eenvoud bevat de uitvoer alleen de IPv4-route tabel.

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

Voer de volgende opdracht uit en gebruik het adres van de interface voor de netwerk bestemming ( `0.0.0.0` ) () `10.0.1.10` in dit voor beeld.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Ondersteuning

Als u na meerdere pogingen geen meta gegevens reactie krijgt, kunt u een ondersteunings probleem in het Azure Portal maken.

## <a name="product-feedback"></a>Productfeedback

U kunt product feedback en ideeën geven aan ons gebruikers feedback kanaal onder Virtual Machines > hier Instance Metadata Service: https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>Volgende stappen

[Een toegangs token voor de virtuele machine ophalen](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Geplande gebeurtenissen voor Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Geplande gebeurtenissen voor Windows](../articles/virtual-machines/windows/scheduled-events.md)
