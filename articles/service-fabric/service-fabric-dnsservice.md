---
title: Azure Service Fabric DNS-service
description: De DNS-service van Service Fabric gebruiken voor het detecteren van micro Services in het cluster.
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91268040"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS Service in Azure Service Fabric
De DNS-service is een optionele systeem service die u in uw cluster kunt inschakelen om andere services te detecteren die gebruikmaken van het DNS-protocol. 

Veel services, met name services die zijn gecontainerd, zijn adresseerbaar via een vooraf bestaande URL. U kunt deze services omzetten met behulp van het standaard-DNS-protocol in plaats van het Service Fabric Naming Service Protocol. Met de DNS-service kunt u DNS-namen toewijzen aan een service naam en daarom IP-adres van het eind punt omzetten. Deze functionaliteit houdt de draag baarheid van services die zijn gecontainerd op verschillende platforms, en kunnen de scenario's van ' lift en verschuiving ' eenvoudiger maken, doordat u bestaande service-Url's kunt gebruiken in plaats van het herschrijven van code om gebruik te maken van de Naming Service. 

De DNS-service wijst DNS-namen toe aan service namen, die op zijn beurt worden omgezet door de Naming Service om het service-eind punt te retour neren. De DNS-naam voor de service wordt verschaft op het moment dat deze wordt gemaakt. In het volgende diagram ziet u hoe de DNS-service werkt voor stateless Services.

![Diagram waarin wordt getoond hoe DNS-namen worden toegewezen aan service namen door de DNS-service voor stateless Services.](./media/service-fabric-dnsservice/stateless-dns.png)

Vanaf Service Fabric versie 6,3 is het Service Fabric DNS-protocol uitgebreid met een schema voor het adresseren van gepartitioneerde stateful Services. Met deze uitbrei dingen kunt u specifieke IP-adressen van partities omzetten met behulp van een combi natie van stateful service DNS-naam en de partitie naam. Alle drie de partitie schema's worden ondersteund:

- Benoemde partitionering
- Partitioneren met bereik
- Singleton-partitionering

In het volgende diagram ziet u hoe de DNS-service werkt voor gepartitioneerde stateful Services.

![Diagram waarin wordt getoond hoe DNS-namen worden toegewezen aan service namen door de DNS-service voor gepartitioneerde stateless Services.](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamische poorten worden niet ondersteund door de DNS-service. Gebruik de [reverse proxy service](./service-fabric-reverseproxy.md)om services die beschikbaar zijn op dynamische poorten, op te lossen.

## <a name="enabling-the-dns-service"></a>De DNS-service inschakelen
> [!NOTE]
> De DNS-service voor Service Fabric Services wordt nog niet ondersteund in Linux.

Wanneer u een cluster maakt met behulp van de portal, wordt de DNS-service standaard ingeschakeld in het selectie vakje **DNS-service insluiten** in het menu **cluster configuratie** :

![DNS-service inschakelen via de portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Als u de portal niet gebruikt om uw cluster te maken of als u een bestaand cluster bijwerkt, moet u de DNS-service inschakelen in een sjabloon:

- Als u een nieuw cluster wilt implementeren, kunt u de [voorbeeld sjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) gebruiken of uw eigen Resource Manager-sjabloon maken. 
- Als u een bestaand cluster wilt bijwerken, gaat u naar de resource groep van het cluster op de portal en klikt u op **Automation script** om te werken met een sjabloon die de huidige status van het cluster en andere resources in de groep weergeeft. Zie [de sjabloon exporteren uit de resource groep](../azure-resource-manager/templates/export-template-portal.md)voor meer informatie.

Nadat u een sjabloon hebt, kunt u de DNS-service inschakelen met de volgende stappen:

1. Controleer of de `apiversion` is ingesteld op `2017-07-01-preview` of hoger voor de `Microsoft.ServiceFabric/clusters` resource, en als dat niet het geval is, werkt u deze bij, zoals wordt weer gegeven in het volgende voor beeld:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Schakel nu de DNS-service in op een van de volgende manieren:

   - Als u de DNS-service met de standaard instellingen wilt inschakelen, voegt u deze toe aan de sectie in de `addonFeatures` `properties` sectie, zoals wordt weer gegeven in het volgende voor beeld:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Als u de service met andere dan de standaard instellingen wilt inschakelen, voegt `DnsService` u een sectie toe aan de `fabricSettings` sectie in de `properties` sectie. In dit geval hoeft u de DNS niet toe te voegen aan `addonFeatures` . Zie [DNS-service-instellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie over de eigenschappen die kunnen worden ingesteld voor de DNS-service.

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. Nadat u de cluster sjabloon met uw wijzigingen hebt bijgewerkt, past u deze toe en laat u de upgrade volt ooien. Wanneer de upgrade is voltooid, wordt de DNS-systeem service gestart in uw cluster. De naam van de service is `fabric:/System/DnsService` en u vindt deze in het gedeelte **systeem** service van service Fabric Explorer. 

> [!NOTE]
> Wanneer de upgrade van DNS van uitgeschakeld naar ingeschakeld is, komt Service Fabric Explorer mogelijk niet overeen met de nieuwe status. Als u dit wilt oplossen, start u de knoop punten opnieuw door de upgrade policy in uw Azure Resource Manager-sjabloon te wijzigen. Zie de [Naslag informatie over service Fabric sjablonen](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) voor meer.

> [!NOTE]
> Wanneer u de DNS-service inschakelt op een lokale computer, worden bepaalde DNS-instellingen genegeerd. Controleer uw DNS-instellingen als u problemen ondervindt bij het maken van verbinding met internet.

## <a name="setting-the-dns-name-for-your-service"></a>De DNS-naam voor uw service instellen
U kunt een DNS-naam voor uw services declaratief instellen voor standaard services in het ApplicationManifest.xml bestand of via Power shell-opdrachten.

De DNS-naam voor uw service kan in het hele cluster worden omgezet. het is belang rijk om ervoor te zorgen dat de DNS-naam in het cluster uniek is. 

Het is raadzaam om een naam schema van `<ServiceDnsName>.<AppInstanceName>` te gebruiken, bijvoorbeeld `service1.application1` . Als een toepassing wordt geïmplementeerd met behulp van docker opstellen, worden aan services automatisch DNS-namen toegewezen met dit naamgevings schema.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Het instellen van de DNS-naam voor een standaard service in de ApplicationManifest.xml
Open uw project in Visual Studio of uw favoriete editor en open het ApplicationManifest.xml-bestand. Ga naar de sectie standaard services en voeg voor elke service het `ServiceDnsName` kenmerk toe. In het volgende voor beeld ziet u hoe u de DNS-naam van de service instelt op `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Zodra de toepassing is geïmplementeerd, wordt in het service-exemplaar in Service Fabric Explorer de DNS-naam voor deze instantie weer gegeven, zoals in de volgende afbeelding: 

![Service-eind punten](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

In het volgende voor beeld wordt de DNS-naam voor een stateful service ingesteld op `statefulsvc.app` . De service maakt gebruik van een benoemde partitie schema. U ziet dat de namen van de partities kleine letters zijn. Dit is een vereiste voor partities die worden bedoeld in DNS-query's; Zie [DNS-Query's maken op een stateful service partitie](#preview-making-dns-queries-on-a-stateful-service-partition)voor meer informatie.

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>De DNS-naam voor een service instellen met behulp van Power shell
U kunt de DNS-naam voor een service instellen wanneer u deze maakt met behulp van de `New-ServiceFabricService` Power shell-opdracht. In het volgende voor beeld wordt een nieuwe stateless service gemaakt met de DNS-naam `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Evaluatie DNS-query's maken op een stateful service partitie
Met ingang van Service Fabric versie 6,3 ondersteunt de Service Fabric DNS-service query's voor service partities.

Voor partities die worden gebruikt in DNS-query's gelden de volgende naamgevings beperkingen:

   - Partitie namen moeten compatibel zijn met DNS.
   - Partitie namen met meerdere labels (met punt, '. ', in naam) mogen niet worden gebruikt.
   - Partitie namen moeten kleine letters zijn.

DNS-query's die zijn gericht op een partitie, zijn als volgt ingedeeld:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Waar:

- Het *eerste label van de gepartitioneerde service-DNSName* is het eerste deel van de DNS-naam van uw service.
- *PartitionPrefix* is een waarde die kan worden ingesteld in de sectie DNS van het cluster manifest of via de Resource Manager-sjabloon van het cluster. De standaard waarde is '--'. Zie  [DNS-service-instellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie.
- *Doel-partitie: naam* is de naam van de partitie. 
- *PartitionSuffix* is een waarde die kan worden ingesteld in de sectie DNS van het cluster manifest of via de Resource Manager-sjabloon van het cluster. De standaard waarde is een lege teken reeks. Zie  [DNS-service-instellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie.
- *Resterend gepartitioneerde service-DNSName* is het resterende deel van de DNS-naam van de service.

In de volgende voor beelden worden DNS-query's weer gegeven voor gepartitioneerde services die worden uitgevoerd op een cluster met standaard instellingen voor `PartitionPrefix` en `PartitionSuffix` : 

- Als u de partitie "0" van een service wilt omzetten met een DNS-naam `backendrangedschemesvc.application` die gebruikmaakt van een partitie schema met bereik, gebruikt u `backendrangedschemesvc-0.application` .
- Als u de partitie ' First ' van een service wilt omzetten met een DNS-naam `backendnamedschemesvc.application` die gebruikmaakt van een benoemde partitie schema, gebruikt u `backendnamedschemesvc-first.application` .

De DNS-service retourneert het IP-adres van de primaire replica van de partitie. Als er geen partitie is opgegeven, retourneert de service het IP-adres van de primaire replica van een wille keurige geselecteerde partitie.

## <a name="using-dns-in-your-services"></a>DNS gebruiken in uw services
Als u meer dan één service implementeert, kunt u de eind punten van andere services vinden om te communiceren met behulp van een DNS-naam. De DNS-service werkt voor stateless Services en, in Service Fabric versie 6,3 en hoger, voor stateful Services. Voor stateful services die worden uitgevoerd op versies van Service Fabric vóór 6,3, kunt u de ingebouwde [reverse proxy-service](./service-fabric-reverseproxy.md) voor http-aanroepen gebruiken om een bepaalde service partitie aan te roepen. 

Dynamische poorten worden niet ondersteund door de DNS-service. U kunt de reverse proxy-service gebruiken voor het oplossen van services die gebruikmaken van dynamische poorten.

De volgende code laat zien hoe u een stateless service aanroept via DNS. Het is gewoon een gewone http-oproep waarbij u de DNS-naam, de poort en een optioneel pad opgeeft als onderdeel van de URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

De volgende code toont een aanroep voor een specifieke partitie van een stateful service. In dit geval bevat de DNS-naam de partitie naam (partition1). Bij de aanroep wordt uitgegaan van een cluster met standaard waarden voor `PartitionPrefix` en `PartitionSuffix` .

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Bekende problemen
* Voor Service Fabric versies 6,3 en hoger is er een probleem met DNS-zoek acties voor service namen met een koppel teken in de DNS-naam. Houd voor meer informatie over dit probleem het volgende github- [probleem](https://github.com/Azure/service-fabric-issues/issues/1197)bij. Er is een oplossing voor dit probleem in de volgende 6,3-update. 

* De DNS-service voor Service Fabric Services wordt nog niet ondersteund in Linux. De DNS-service wordt ondersteund voor containers in Linux. Hand matige omzetting met Fabric client/ServicePartitionResolver is het beschik bare alternatief.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over service communicatie binnen het cluster met  [verbinding maken en communiceren met Services](service-fabric-connect-and-communicate-with-services.md)
