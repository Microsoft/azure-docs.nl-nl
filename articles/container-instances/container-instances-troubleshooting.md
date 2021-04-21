---
title: Algemene problemen
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het implementeren, uitvoeren of beheren van Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ce7e3018e470df3840eb01127a7bf2ffa01b5cbc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771061"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Veelvoorkomende problemen in Azure Container Instances oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende problemen voor het beheren of implementeren van containers in Azure Container Instances. Zie ook [Veelgestelde vragen.](container-instances-faq.md)

Als u aanvullende ondersteuning nodig hebt, bekijkt u de beschikbare **help-** en [ondersteuningsopties](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in Azure Portal .

## <a name="issues-during-container-group-deployment"></a>Problemen tijdens de implementatie van containergroep
### <a name="naming-conventions"></a>Naamconventies

Bij het definiëren van uw containerspecificatie moeten bepaalde parameters voldoen aan naamgevingsbeperkingen. Hieronder vindt u een tabel met specifieke vereisten voor eigenschappen van containergroep. Zie [Naming conventions][azure-name-restrictions] in the Azure Architecture Center and Naming rules and restrictions for Azure resources (Naamgevingsregels en naamgevingsregels en -beperkingen [voor Azure-resources) voor meer informatie.][naming-rules]

| Bereik | Lengte | Hoofdlettergebruik | Geldige tekens | Voorgesteld patroon | Voorbeeld |
| --- | --- | --- | --- | --- | --- |
| Containernaam<sup>1</sup> | 1-63 |Kleine letters | Alfanumeriek en afbreekstreetine overal behalve het eerste of laatste teken |`<name>-<role>-container<number>` |`web-batch-container1` |
| Containerpoorten | Tussen 1 en 65535 |Geheel getal |Geheel getal tussen 1 en 65535 |`<port-number>` |`443` |
| DNS-naamlabel | 5-63 |Niet hoofdlettergevoelig |Alfanumeriek en afbreekstreetine overal behalve het eerste of laatste teken |`<name>` |`frontend-site1` |
| Omgevingsvariabele | 1-63 |Niet hoofdlettergevoelig |Alfanumeriek en onderstrepingsteken (_) overal behalve het eerste of laatste teken |`<name>` |`MY_VARIABLE` |
| Volumenaam | 5-63 |Kleine letters |Alfanumeriek en afbreekstreeken overal behalve het eerste of laatste teken. Mag geen twee opeenvolgende afbreekstreeepten bevatten. |`<name>` |`batch-output-volume` |

<sup>1</sup> Beperking geldt ook voor containergroepnamen wanneer deze niet onafhankelijk van containerin instances zijn opgegeven, bijvoorbeeld met `az container create` opdrachtimplementaties.

### <a name="os-version-of-image-not-supported"></a>Versie van het besturingssysteem van de afbeelding wordt niet ondersteund

Als u een afbeelding opgeeft die Azure Container Instances niet ondersteunt, wordt `OsVersionNotSupported` er een fout geretourneerd. De fout is vergelijkbaar met de volgende, waarbij de naam is van de `{0}` afbeelding die u hebt geïmplementeerd:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Deze fout tkomt meestal aan bij het implementeren van Windows-afbeeldingen die zijn gebaseerd op Semi-Annual Channel release 1709 of 1803, die niet worden ondersteund. Zie Veelgestelde vragen Azure Container Instances voor [ondersteunde Windows-Azure Container Instances.](container-instances-faq.md#what-windows-base-os-images-are-supported)

### <a name="unable-to-pull-image"></a>Kan afbeelding niet pullen

Als Azure Container Instances de afbeelding in eerste instantie niet kan worden opgevraagd, wordt er een bepaalde tijd opnieuw een nieuwe keer een nieuwe pull-instantie onder de naam . Als de pull-bewerking van de installatie afbeelding blijft mislukken, mislukt ACI uiteindelijk de implementatie en ziet u mogelijk een `Failed to pull image` fout.

U kunt dit probleem oplossen door de containerin instance te verwijderen en de implementatie opnieuw uit te proberen. Zorg ervoor dat de afbeelding in het register bestaat en dat u de naam van de afbeelding correct hebt getypt.

Als de afbeelding niet kan worden opgetrokken, worden gebeurtenissen zoals de volgende weergegeven in de uitvoer van [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type&quot;: &quot;Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type&quot;: &quot;Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type&quot;: &quot;Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Fout: Resource niet beschikbaar

Als gevolg van wisselende regionale resourcebelasting in Azure, kan de volgende fout worden weergegeven bij het implementeren van een container-instantie:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Deze fout geeft aan dat vanwege de zware belasting in de regio waarin u probeert te implementeren, de resources die zijn opgegeven voor uw container op dat moment niet kunnen worden toegewezen. Gebruik een of meer van de volgende oplossingsstappen om uw probleem op te lossen.

* Controleer of de instellingen voor de containerimplementatie binnen de parameters vallen die zijn gedefinieerd in [Regiobeschikbaarheid voor Azure Container Instances](container-instances-region-availability.md)
* Lagere CPU- en geheugeninstellingen voor de container opgeven
* Implementeren in een andere Azure-regio
* Implementeren op een later tijdstip

## <a name="issues-during-container-group-runtime"></a>Problemen tijdens runtime van containergroep
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Container wordt voortdurend afgesloten en opnieuw gestart (geen langlopend proces)

Containergroepen worden standaard ingesteld op [een beleid voor opnieuw opstarten](container-instances-restart-policy.md) van **Always,** zodat containers in de containergroep altijd opnieuw worden opgestart nadat ze zijn voltooid. Mogelijk moet u dit wijzigen in **OnFailure** of **Nooit als** u op taken gebaseerde containers wilt uitvoeren. Als u **OnFailure opgeeft en** continu opnieuw opstarten blijft zien, is er mogelijk een probleem met de toepassing of het script dat in uw container wordt uitgevoerd.

Bij het uitvoeren van containergroepen zonder langlopende processen ziet u mogelijk herhaalde exits en opnieuw opstarten met afbeeldingen zoals Ubuntu of Alpine. Verbinding maken via [EXEC](container-instances-exec.md) werkt niet omdat de container geen proces heeft om de container in leven te houden. U kunt dit probleem oplossen door een startopdracht zoals de volgende op te nemen bij de implementatie van uw containergroep om de container actief te houden.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

De Container Instances API en Azure Portal bevat een `restartCount` eigenschap. U kunt de opdracht [az container show][az-container-show] in de Azure CLI gebruiken om het aantal herstarts voor een container te controleren. In de volgende voorbeelduitvoer (die voor de beknoptheid is afgekapt), ziet u de eigenschap aan het `restartCount` einde van de uitvoer.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> De meeste container-afbeeldingen voor Linux-distributies stellen een shell, zoals bash, in als de standaardopdracht. Omdat een shell op zichzelf geen langlopende service is, worden deze containers onmiddellijk afgesloten en in een lus voor opnieuw opstarten geplaatst wanneer deze zijn geconfigureerd met het standaardbeleid **Altijd** opnieuw opstarten.

### <a name="container-takes-a-long-time-to-start"></a>Het starten van de container duurt lang

De drie belangrijkste factoren die bijdragen aan de opstarttijd van containers in Azure Container Instances zijn:

* [Afbeeldingsgrootte](#image-size)
* [Locatie van afbeelding](#image-location)
* [In cache opgeslagen afbeeldingen](#cached-images)

Windows-afbeeldingen [hebben aanvullende overwegingen.](#cached-images)

#### <a name="image-size"></a>Afbeeldingsgrootte

Als het lang duurt voordat uw container is begonnen, maar uiteindelijk slaagt, kijkt u eerst naar de grootte van uw containerafbeelding. Omdat Azure Container Instances container-afbeelding op aanvraag opvraagt, is de opstarttijd die u ziet direct gerelateerd aan de grootte.

U kunt de grootte van uw containerafbeelding weergeven met behulp van `docker images` de opdracht in de Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

De sleutel om de grootte van afbeeldingen klein te houden, is ervoor te zorgen dat uw uiteindelijke afbeelding niets bevat dat niet vereist is tijdens runtime. Een manier om dit te doen is met [builds met meerdere fases.][docker-multi-stage-builds] Met builds met meerdere fases kunt u er eenvoudig voor zorgen dat de uiteindelijke afbeelding alleen de artefacten bevat die u nodig hebt voor uw toepassing, en niet een van de extra inhoud die nodig was tijdens de build.

#### <a name="image-location"></a>Locatie van afbeelding

Een andere manier om de impact van de pull van de afbeelding op de opstarttijd van uw container te verminderen, is door de containerafbeelding te hosten in [Azure Container Registry](../container-registry/index.yml) in dezelfde regio waar u container-exemplaren wilt implementeren. Hierdoor wordt het netwerkpad dat de containerafbeelding nodig heeft, aanzienlijk verkort, wat de downloadtijd aanzienlijk verkort.

#### <a name="cached-images"></a>Afbeeldingen in cache

Azure Container Instances maakt gebruik van een caching-mechanisme om de opstarttijd van containers te versnellen voor afbeeldingen die zijn gebouwd op algemene [Windows-basisafbeeldingen,](container-instances-faq.md#what-windows-base-os-images-are-supported)waaronder `nanoserver:1809` , en `servercore:ltsc2019` `servercore:1809` . Veelgebruikte Linux-afbeeldingen, `ubuntu:1604` zoals `alpine:3.6` en, worden ook in de cache opgeslagen. Vermijd het gebruik van de tag voor zowel Windows- als `latest` Linux-afbeeldingen. Bekijk Container Registry procedures voor het [taggen van afbeeldingen voor](../container-registry/container-registry-image-tag-version.md) hulp. Gebruik de API List [Cached Images][list-cached-images] voor een actuele lijst met afbeeldingen en tags in de cache.

> [!NOTE]
> Het gebruik van installatiekopieën op basis van Windows Server 2019 in Azure Container Instances bevindt zich nog in de preview-fase.

#### <a name="windows-containers-slow-network-readiness"></a>Trage netwerk gereedheid van Windows-containers

Bij het maken van de eerste keer hebben Windows-containers mogelijk tot 30 seconden (in zeldzame gevallen) geen binnenkomende of uitgaande connectiviteit. Als uw containertoepassing een internetverbinding nodig heeft, voegt u vertraging en pogingslogica toe om 30 seconden internetverbinding tot stand te brengen. Na de eerste installatie moeten containernetwerken op de juiste wijze worden hervat.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Kan geen verbinding maken met onderliggende Docker-API of bevoegde containers uitvoeren

Azure Container Instances biedt geen directe toegang tot de onderliggende infrastructuur die als host voor containergroepen wordt geplaatst. Dit omvat toegang tot de Docker-API die wordt uitgevoerd op de host van de container en het uitvoeren van bevoegde containers. Als u Docker-interactie nodig hebt, raadpleegt u de [REST-referentiedocumentatie](/rest/api/container-instances/) om te zien wat de ACI API ondersteunt. Als er iets ontbreekt, dient u een aanvraag in op de [ACI-feedbackforums.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Het IP-adres van de containergroep is mogelijk niet toegankelijk vanwege niet-overeenkomende poorten

Azure Container Instances biedt nog geen ondersteuning voor poorttoewijzing, zoals bij een normale Docker-configuratie. Als u vindt dat het IP-adres van een containergroep niet toegankelijk is wanneer u denkt dat dit moet zijn, controleert u of u de containerafbeelding hebt geconfigureerd om te luisteren naar dezelfde poorten die u beschikbaar maakt in uw containergroep met de eigenschap `ports` .

Als u wilt bevestigen dat Azure Container Instances kunt luisteren op de poort die u hebt geconfigureerd in uw containerafbeelding, test u een implementatie van de installatie afbeelding die `aci-helloworld` de poort toont. Voer de `aci-helloworld` app ook uit zodat deze op de poort luistert. `aci-helloworld` accepteert een optionele omgevingsvariabele om de standaardpoort 80 te overschrijven `PORT` die wordt gebruikt. Als u bijvoorbeeld poort 9000 wilt testen, stelt u de [omgevingsvariabele in](container-instances-environment-variables.md) wanneer u de containergroep maakt:

1. Stel de containergroep in om poort 9000 weer te geven en geef het poortnummer door als de waarde van de omgevingsvariabele. Het voorbeeld is opgemaakt voor de Bash-shell. Als u de voorkeur geeft aan een andere shell, zoals PowerShell of de opdrachtprompt, moet u de variabeletoewijzing dienovereenkomstig aanpassen.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Zoek het IP-adres van de containergroep in de opdrachtuitvoer van `az container create` . Zoek naar de waarde van **ip**. 
1. Nadat de container is ingericht, bladert u naar het IP-adres en de poort van de container-app in uw browser, bijvoorbeeld: `192.0.2.0:9000` . 

    U ziet nu het 'Welkom bij Azure Container Instances!' weergegeven door de web-app.
1. Wanneer u klaar bent met de container, verwijdert u deze met de `az container delete` opdracht :

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ophalen van containerlogboeken en -gebeurtenissen](container-instances-get-logs.md) om fouten in uw containers op te sporen.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
