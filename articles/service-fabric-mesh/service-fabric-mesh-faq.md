---
title: Veelgestelde vragen over Azure Service Fabric mesh
description: Meer informatie over veelgestelde vragen en antwoorden voor Azure Service Fabric net.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626033"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Veelgestelde vragen over Service Fabric mesh

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. In dit artikel vindt u antwoorden op veelgestelde vragen.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hoe kan ik een probleem melden of een vraag stellen?

Stel vragen, krijg antwoorden van micro soft-technici en Meld problemen met de [service-Fabric-mesh-preview github opslag plaats](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Quota en kosten

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Wat zijn de kosten voor deelname aan de preview?

Er worden momenteel geen kosten in rekening gebracht voor het implementeren van toepassingen of containers naar de mesh-preview. Kijk of er updates in mogelijk zijn voor de facturering. We raden u echter aan om de resources die u implementeert, te verwijderen, tenzij u ze actief testen.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Is er een quotum limiet van het aantal kern geheugens en RAM?

Ja. De quota's voor elk abonnement zijn:

- Aantal toepassingen: 5
- Kernen per toepassing: 12
- Totaal RAM per toepassing: 48 GB
- Eind punten voor netwerk en ingang: 5
- Azure-volumes die u kunt koppelen: 10
- Aantal service replica's: 3
- De grootste container die u kunt implementeren, is beperkt tot 4 kernen en 16 GB RAM-geheugen.
- U kunt gedeeltelijke kernen toewijzen aan uw containers in stappen van 0,5 kernen, Maxi maal 6 kernen.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Hoe lang kan ik mijn toepassing laten implementeren?

De levens duur van een toepassing is momenteel beperkt tot twee dagen. Dit is om het gebruik te maximaliseren van de gratis kernen die aan de preview-versie zijn toegewezen. Als gevolg hiervan is het alleen toegestaan om een bepaalde implementatie gedurende 48 uur continu uit te voeren, waarna de tijd wordt afgesloten.

Als dit het geval is, kunt u valideren dat het systeem is afgesloten door de `az mesh app show` opdracht uit te voeren in de Azure cli. Controleren of deze retourneert `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Bijvoorbeeld: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Als u de resource groep wilt verwijderen, gebruikt u de `az group delete <nameOfResourceGroup>` opdracht.

## <a name="deployments"></a>Implementaties

### <a name="what-container-images-are-supported"></a>Welke container installatie kopieën worden ondersteund?

Als u ontwikkelt op een update van Windows najaar Crea tors (versie 1709), kunt u alleen Windows versie 1709 docker-installatie kopieën gebruiken.

Als u op een computer met Windows 10 april 2018 (versie 1803) ontwikkelt, kunt u Windows-versie 1709 of Windows-versie 1803 docker-installatie kopieën gebruiken.

De volgende container besturingssysteem installatie kopieën kunnen worden gebruikt voor het implementeren van services:
- Windows-windowsservercore en nano server
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Geen bekende beperkingen

> [!NOTE]
> Visual Studio-hulp middelen voor net biedt nog geen ondersteuning voor de implementatie in Windows Server 2019-en 1809-containers.

### <a name="what-types-of-applications-can-i-deploy"></a>Welke typen toepassingen kan ik implementeren? 

U kunt alles implementeren dat wordt uitgevoerd in containers die passen binnen de beperkingen die zijn ingesteld voor een toepassings bron (zie hierboven voor meer informatie over quota's). Als we detecteren dat u net gebruikt voor het uitvoeren van illegale werk belastingen of het beledigen van het systeem (d.w.z. mijn bouw), behouden wij ons het recht voor uw implementaties te beëindigen en blokkerings lijst uw abonnement uit te voeren op de service. Neem contact met ons op als u vragen hebt over het uitvoeren van een bepaalde werk belasting. 

## <a name="developer-experience-issues"></a>Problemen met ontwikkelaars ervaring

### <a name="dns-resolution-from-a-container-doesnt-work"></a>DNS-omzetting vanuit een container werkt niet

Uitgaande DNS-query's van een container naar de Service Fabric DNS-service kunnen in bepaalde omstandigheden mislukken. Dit wordt onderzocht. Beperken:

- Gebruik Windows najaar Creators update (versie 1709) of hoger als uw basis container installatie kopie.
- Als de service naam alleen werkt, probeert u de volledig gekwalificeerde naam: ServiceName. ApplicationName.
- Voeg in het docker-bestand voor uw service `EXPOSE <port>` de poort toe waar u uw service beschikbaar maakt. Bijvoorbeeld:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS werkt niet hetzelfde als voor Service Fabric-ontwikkelings clusters en in het net

Mogelijk moet u in uw lokale ontwikkel cluster op een andere manier verwijzen naar services dan in azure mesh.

Gebruik in uw lokale ontwikkel cluster `{serviceName}.{applicationName}` . Gebruik in azure Service Fabric net `{servicename}` . 

Azure mesh biedt momenteel geen ondersteuning voor DNS-omzetting in toepassingen.

Zie voor andere bekende DNS-problemen met het uitvoeren van een Service Fabric-ontwikkelings cluster in Windows 10: [fout opsporing in Windows-containers](../service-fabric/service-fabric-how-to-debug-windows-containers.md) en [bekende DNS-problemen](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Netwerken

De NAT van het ServiceFabric-netwerk verdwijnt wanneer u uw app uitvoert op uw lokale machine. Als u wilt vaststellen of dit is gebeurd, voert u het volgende uit vanaf een opdracht prompt:

`docker network ls` en noteer of `servicefabric_nat` wordt weer gegeven.  Als dat niet het geval is, voert u de volgende opdracht uit: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Hiermee wordt het probleem opgelost, zelfs wanneer de app al lokaal en in een slechte staat wordt geïmplementeerd.

### <a name="issues-running-multiple-apps"></a>Problemen met het uitvoeren van meerdere apps

U kunt de CPU-Beschik baarheid en de limieten voor alle toepassingen vast. Beperken:
- Maak een cluster met vijf knoop punten.
- Verminder het CPU-gebruik in Services in de app die is geïmplementeerd. Wijzig bijvoorbeeld in het bestand service. yaml van uw `cpu: 1.0` service in `cpu: 0.5`

Meerdere toepassingen kunnen niet worden geïmplementeerd op een cluster met één knoop punt. Beperken:
- Gebruik een cluster met vijf knoop punten wanneer u meerdere apps implementeert op een lokaal cluster.
- Apps verwijderen die momenteel niet worden getest.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS-hulp middelen heeft beperkte ondersteuning voor Windows-containers

Visual Studio-hulpprogram ma's bieden alleen ondersteuning voor het implementeren van Windows-containers met een basis besturingssysteem versie van Windows Server 1709 en 1803 vandaag. 

## <a name="feature-gaps-and-other-known-issues"></a>Hiaten van functies en andere bekende problemen

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Na de implementatie van mijn toepassing heeft de netwerk bron die eraan is gekoppeld, geen IP-adres

Er is een bekend probleem waarbij het IP-adres niet onmiddellijk beschikbaar is. Controleer de status van de netwerk bron in een paar minuten om het bijbehorende IP-adres te zien.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Mijn toepassing heeft geen toegang tot de juiste netwerk/volume bron

Gebruik in uw toepassings model de volledige Resource-ID voor netwerken en volumes om toegang te krijgen tot de bijbehorende resource. Hier volgt een voor beeld van het Quick start-voor beeld:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Wanneer ik uitschaal, worden alle containers beïnvloed, inclusief het uitvoeren van

Dit is een bug en er wordt een oplossing geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht](service-fabric-mesh-overview.md)voor meer informatie over service Fabric net.
