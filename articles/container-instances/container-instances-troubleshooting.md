---
title: Algemene problemen
description: Meer informatie over het oplossen van veelvoorkomende problemen wanneer u Azure Container Instances implementeert, uitvoert of beheert
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779512"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Veelvoorkomende problemen in Azure Container Instances oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende problemen oplost voor het beheren of implementeren van containers in Azure Container Instances. Zie ook [Veelgestelde vragen](container-instances-faq.md).

Als u aanvullende ondersteuning nodig hebt, raadpleegt u de beschik bare **Help en ondersteunings** opties in de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemen tijdens de implementatie van de container groep
### <a name="naming-conventions"></a>Naamconventies

Bij het definiëren van de container specificatie, moeten bepaalde para meters worden uitgebreid tot naamgevings beperkingen. Hieronder ziet u een tabel met specifieke vereisten voor container groep eigenschappen. Zie [naamgevings conventies][azure-name-restrictions] in de Azure Architecture Center en [naamgevings regels en beperkingen voor Azure-resources][naming-rules]voor meer informatie.

| Bereik | Lengte | Hoofdlettergebruik | Geldige tekens | Voorgesteld patroon | Voorbeeld |
| --- | --- | --- | --- | --- | --- |
| Container naam<sup>1</sup> | 1-63 |Kleine letters | Alfanumerieke tekens en afbreek streepjes, behalve het eerste of laatste teken |`<name>-<role>-container<number>` |`web-batch-container1` |
| Container poorten | Tussen 1 en 65535 |Geheel getal |Geheel getal tussen 1 en 65535 |`<port-number>` |`443` |
| DNS-naamlabel | 5-63 |Niet hoofdlettergevoelig |Alfanumerieke tekens en afbreek streepjes, behalve het eerste of laatste teken |`<name>` |`frontend-site1` |
| Omgevingsvariabele | 1-63 |Niet hoofdlettergevoelig |Alfanumerieke tekens en onderstrepings tekens (_) overal behalve het eerste of laatste teken |`<name>` |`MY_VARIABLE` |
| Volumenaam | 5-63 |Kleine letters |Alfanumerieke tekens en afbreek streepjes, behalve het eerste of laatste teken. Kan niet twee opeenvolgende afbreek streepjes bevatten. |`<name>` |`batch-output-volume` |

<sup>1</sup> Beperking ook voor container groeps namen wanneer deze niet onafhankelijk van container instanties worden opgegeven, bijvoorbeeld met `az container create` opdracht implementaties.

### <a name="os-version-of-image-not-supported"></a>De versie van het besturings systeem van de installatie kopie wordt niet ondersteund

Als u een installatie kopie opgeeft die Azure Container Instances niet wordt ondersteund, `OsVersionNotSupported` wordt een fout geretourneerd. De fout is vergelijkbaar met de volgende, waarbij `{0}` de naam is van de installatie kopie die u probeert te implementeren:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Deze fout treedt meestal op bij het implementeren van Windows-installatie kopieën die zijn gebaseerd op Semi-Annual kanaal release 1709 of 1803, wat niet wordt ondersteund. Zie [Veelgestelde vragen](container-instances-faq.md#what-windows-base-os-images-are-supported)voor ondersteunde Windows-installatie kopieën in azure container instances.

### <a name="unable-to-pull-image"></a>Kan de installatie kopie niet ophalen

Als Azure Container Instances in eerste instantie de afbeelding niet kan ophalen, wordt er gedurende een periode een nieuwe poging gedaan. Als de pull-bewerking van de installatie kopie blijft mislukken, mislukt het uitvoeren van de implementatie en ziet u mogelijk een `Failed to pull image` fout melding.

U kunt dit probleem oplossen door het container exemplaar te verwijderen en de implementatie opnieuw uit te voeren. Zorg ervoor dat de installatie kopie in het REGI ster bestaat en dat u de naam van de installatie kopie correct hebt getypt.

Als de installatie kopie niet kan worden opgehaald, worden gebeurtenissen zoals de volgende weer gegeven in de uitvoer van [AZ container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Fout bericht bron niet beschikbaar

Als gevolg van verschillende regionale resource belasting in azure, wordt mogelijk de volgende fout weer gegeven bij het implementeren van een container exemplaar:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Deze fout geeft aan dat de resources die zijn opgegeven voor de container niet op dat moment kunnen worden toegewezen vanwege een zware belasting in de regio waarin u wilt implementeren. Gebruik een of meer van de volgende beperkings stappen om het probleem op te lossen.

* Controleer of de implementatie-instellingen van de container vallen binnen de para meters die zijn gedefinieerd in de [regio beschikbaarheid voor Azure container instances](container-instances-region-availability.md)
* Lagere CPU-en geheugen instellingen voor de container opgeven
* Implementeren naar een andere Azure-regio
* Op een later tijdstip implementeren

## <a name="issues-during-container-group-runtime"></a>Problemen tijdens runtime van container groep
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Container wordt voortdurend afgesloten en opnieuw gestart (geen langlopend proces)

Container groepen worden standaard ingesteld op het beleid **voor** [opnieuw opstarten](container-instances-restart-policy.md) , zodat containers in de container groep altijd opnieuw worden opgestart nadat ze zijn uitgevoerd om te worden voltooid. Mogelijk moet u dit wijzigen in **OnFailure** of **nooit** als u van plan bent om op taken gebaseerde containers uit te voeren. Als u **OnFailure** opgeeft en nog steeds voortdurend opnieuw wordt opgestart, is er mogelijk een probleem met de toepassing of het script dat in uw container wordt uitgevoerd.

Bij het uitvoeren van container groepen zonder langlopende processen ziet u mogelijk herhaalde afsluit bewerkingen en wordt de computer opnieuw opgestart met installatie kopieën zoals Ubuntu of Alpine. Het maken van een verbinding via [exec](container-instances-exec.md) werkt niet omdat de container geen proces heeft dat deze actief houdt. Als u dit probleem wilt oplossen, voegt u een start opdracht toe, zoals in het volgende voor de container groeps implementatie, om ervoor te zorgen dat de container wordt uitgevoerd.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

De Container Instances-API en Azure Portal bevatten een `restartCount` eigenschap. Als u het aantal herstartingen voor een container wilt controleren, kunt u de opdracht [AZ container show][az-container-show] gebruiken in de Azure cli. In de volgende voorbeeld uitvoer (die is afgekapt voor de boog), ziet u de `restartCount` eigenschap aan het einde van de uitvoer.

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
> De meeste container installatie kopieën voor Linux-distributies stellen een shell, zoals bash, in als de standaard opdracht. Omdat een shell op zichzelf geen langlopende service is, worden deze containers onmiddellijk afgesloten en in een restart-lus gezet wanneer deze is geconfigureerd met het standaard beleid voor **altijd** opnieuw opstarten.

### <a name="container-takes-a-long-time-to-start"></a>Het starten van de container duurt lang

De drie primaire factoren die bijdragen aan de opstart tijd van de container in Azure Container Instances zijn:

* [Afbeeldings grootte](#image-size)
* [Locatie van installatie kopie](#image-location)
* [Afbeeldingen in cache](#cached-images)

Windows-installatie kopieën hebben [extra overwegingen](#cached-images).

#### <a name="image-size"></a>Afbeeldings grootte

Als uw container veel tijd in beslag neemt, maar uiteindelijk is geslaagd, gaat u eerst naar de grootte van uw container installatie kopie. Omdat Azure Container Instances uw container installatie kopie op aanvraag ophaalt, is de opstart tijd die u ziet direct gerelateerd aan de grootte.

U kunt de grootte van uw container installatie kopie weer geven met behulp van de `docker images` opdracht in de docker-cli:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

De sleutel voor het bewaren van afbeeldings grootten zorgt ervoor dat de uiteindelijke afbeelding niets hoeft te bevatten die tijdens runtime niet is vereist. Een manier om dit te doen is met [meerdere fase builds][docker-multi-stage-builds]. Met meerdere fasen kunt u ervoor zorgen dat de uiteindelijke installatie kopie alleen de artefacten bevat die u nodig hebt voor uw toepassing, en niet een van de extra inhoud die tijdens het bouwen was vereist.

#### <a name="image-location"></a>Locatie van installatie kopie

Een andere manier om de impact van de installatie kopie op de opstart tijd van de container te verminderen, is door de container installatie kopie te hosten in [Azure container Registry](../container-registry/index.yml) in dezelfde regio waarin u container instanties wilt implementeren. Hiermee verkort u het netwerkpad dat de container installatie kopie moet reizen, waardoor de download tijd aanzienlijk verkort.

#### <a name="cached-images"></a>Afbeeldingen in cache

Azure Container Instances gebruikt een cache mechanisme om de opstart tijd van de container te versnellen voor installatie kopieën die zijn gebaseerd op algemene [Windows-basis installatie kopieën](container-instances-faq.md#what-windows-base-os-images-are-supported), waaronder `nanoserver:1809` , `servercore:ltsc2019` en `servercore:1809` . Veelgebruikte Linux-installatie kopieën, zoals `ubuntu:1604` en `alpine:3.6` , worden ook in de cache opgeslagen. Vermijd het gebruik van de tag voor zowel Windows-als Linux-installatie kopieën `latest` . Bekijk de [Aanbevolen procedures voor de beeld code](../container-registry/container-registry-image-tag-version.md) van container Registry voor hulp. Voor een bijgewerkte lijst met afbeeldingen en tags in de cache gebruikt u de API-afbeelding van de [lijst in cache][list-cached-images] .

> [!NOTE]
> Het gebruik van installatiekopieën op basis van Windows Server 2019 in Azure Container Instances bevindt zich nog in de preview-fase.

#### <a name="windows-containers-slow-network-readiness"></a>Windows-containers langzaam netwerk gereedheid

Na het maken van de eerste keer is het mogelijk dat Windows-containers tot 30 seconden (of langer) in zeldzame gevallen geen binnenkomende of uitgaande connectiviteit hebben. Als uw container toepassing een Internet verbinding nodig heeft, voegt u vertraging toe en voert u de logica opnieuw uit om 30 seconden een Internet verbinding tot stand te brengen. Na de initiële installatie moet de container netwerken op de juiste manier worden hervat.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Kan geen verbinding maken met de onderliggende docker-API of er worden geprivilegieerde containers uitgevoerd

Azure Container Instances biedt geen rechtstreekse toegang tot de onderliggende infra structuur die als host fungeert voor container groepen. Dit geldt ook voor toegang tot de docker-API die wordt uitgevoerd op de host van de container en het uitvoeren van geprivilegieerde containers. Als u docker-interactie nodig hebt, raadpleegt u de [rest-referentie documentatie](/rest/api/container-instances/) om te zien wat de ACI API ondersteunt. Als er iets ontbreekt, dient u een aanvraag in te dienen bij de forums over de [ACI-feedback](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Het IP-adres van de containergroep is mogelijk niet toegankelijk vanwege niet-overeenkomende poorten

Azure Container Instances biedt nog geen ondersteuning voor poort toewijzing, zoals bij normale docker-configuratie. Als u het IP-adres van een container groep niet toegankelijk hebt wanneer u denkt dat dit het geval is, moet u ervoor zorgen dat u de container installatie kopie hebt geconfigureerd om te Luis teren naar dezelfde poorten die u in uw container groep beschikbaar maakt met de `ports` eigenschap.

Als u wilt bevestigen dat Azure Container Instances kan Luis teren op de poort die u in de container installatie kopie hebt geconfigureerd, test u een implementatie van de `aci-helloworld` installatie kopie die de poort beschikbaar maakt. Voer de app ook uit `aci-helloworld` zodat deze op de poort luistert. `aci-helloworld` Hiermee wordt een optionele omgevings variabele geaccepteerd `PORT` voor het overschrijven van de standaard poort 80 die wordt geluisterd. Als u bijvoorbeeld poort 9000 wilt testen, stelt u de [omgevings variabele](container-instances-environment-variables.md) in wanneer u de container groep maakt:

1. Stel de container groep in om poort 9000 beschikbaar te maken en geef het poort nummer door als de waarde van de omgevings variabele. Het voor beeld is opgemaakt voor de bash-shell. Als u de voor keur geeft aan een andere shell, zoals Power shell of opdracht prompt, moet u de toewijzing van de variabele dienovereenkomstig aanpassen.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Zoek het IP-adres van de container groep in de uitvoer van de opdracht van `az container create` . Zoek de waarde van **IP**. 
1. Nadat de container is ingericht, bladert u naar het IP-adres en de poort van de container-app in uw browser, bijvoorbeeld: `192.0.2.0:9000` . 

    U ziet de ' Welkom bij Azure Container Instances! ' het bericht dat wordt weer gegeven door de web-app.
1. Wanneer u klaar bent met de container, verwijdert u deze met behulp van de `az container delete` opdracht:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ophalen van container logboeken en-gebeurtenissen](container-instances-get-logs.md) voor het opsporen van fouten in uw containers.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
