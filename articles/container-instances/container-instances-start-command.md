---
title: Entry point in container exemplaar overschrijven
description: Een opdracht regel instellen om het toegangs punt in een container installatie kopie te overschrijven wanneer u een Azure container-exemplaar implementeert
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169626"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Stel de opdracht regel in een container exemplaar in om de standaard opdracht regel bewerking te overschrijven

Wanneer u een container exemplaar maakt, geeft u optioneel een opdracht op om de standaard opdracht regel instructie geïntegreerde in de container installatie kopie te overschrijven. Dit gedrag is vergelijkbaar met het `--entrypoint` opdracht regel argument voor `docker run` .

Net als bij het instellen van [omgevings variabelen](container-instances-environment-variables.md) voor container instanties is het opgeven van een start opdracht regel handig voor batch taken waarbij elke container dynamisch moet worden voor bereid met een taak-specifieke configuratie.

## <a name="command-line-guidelines"></a>Opdracht regel richtlijnen

* De opdracht regel specificeert standaard *één proces dat wordt gestart zonder een shell* in de container. De opdracht regel kan bijvoorbeeld een python-script of een uitvoerbaar bestand worden uitgevoerd. Het proces kan aanvullende para meters of argumenten opgeven.

* Als u meerdere opdrachten wilt uitvoeren, start u de opdracht regel door een shell-omgeving in te stellen die wordt ondersteund in het besturings systeem van de container. Voorbeelden:

  |Besturingssysteem  |Standaard shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Volg de conventies van de shell om meerdere opdrachten te combi neren die in volg orde moeten worden uitgevoerd.

* Afhankelijk van de configuratie van de container, moet u mogelijk een volledig pad naar het uitvoer bare bestand van de opdracht regel of argumenten instellen.

* Stel een geschikt [beleid voor opnieuw opstarten](container-instances-restart-policy.md) in voor het container exemplaar, afhankelijk van het feit of de opdracht regel een langlopende taak of een taak die wordt uitgevoerd. Een voor beeld: een beleid voor opnieuw opstarten `Never` of `OnFailure` wordt aanbevolen voor een Run-Once-taak. 

* Als u meer informatie nodig hebt over de standaardset in een container installatie kopie, gebruikt u de opdracht voor het [inspecteren van de docker-installatie kopie](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Opdracht regel syntaxis

De syntaxis van de opdracht regel is afhankelijk van de API of het hulp programma van Azure dat wordt gebruikt om de instanties te maken. Als u een shell-omgeving opgeeft, moet u ook rekening houden met de opdracht syntaxis conventies van de shell.

* [AZ container Create][az-container-create] opdracht: Geef een teken reeks door met de `--command-line` para meter. Voor beeld: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell-cmdlet: Geef een teken reeks door met de `-Command` para meter. Bijvoorbeeld: `-Command "echo hello"`.

* Azure Portal: Geef in de **opdracht override** van de container configuratie een door komma's gescheiden lijst met teken reeksen op, zonder aanhalings tekens. Voor beeld: `python, myscript.py, arg1, arg2` ). 

* Resource Manager-sjabloon of YAML-bestand of een van de Azure-Sdk's: Geef de opdracht regel eigenschap op als een matrix met teken reeksen. Voor beeld: de JSON-matrix `["python", "myscript.py", "arg1", "arg2"]` in een resource manager-sjabloon. 

  Als u bekend bent met de [Dockerfile](https://docs.docker.com/engine/reference/builder/) -syntaxis, is deze indeling vergelijkbaar met het formulier *Raad* van de cmd-instructie.

### <a name="examples"></a>Voorbeelden

|    |  Azure CLI   | Portal | Sjabloon | 
| ---- | ---- | --- | --- |
| **Eén opdracht** | `--command-line "python myscript.py arg1 arg2"` | **Opdracht negeren**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Meerdere opdrachten** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Opdracht negeren**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Voor beeld van Azure CLI

Wijzig bijvoorbeeld het gedrag van de container installatie kopie [micro soft/ACI-WordCount][aci-wordcount] , die tekst analyseert in de *Hamlet* van Shakespeare om de meest voorkomende woorden te vinden. In plaats van *Hamlet* te analyseren, kunt u een opdracht regel instellen die naar een andere tekst bron wijst.

Als u de uitvoer van de [micro soft/ACI-WordCount-][aci-wordcount] container wilt zien bij het analyseren van de standaard tekst, voert u deze uit met de volgende opdracht [AZ container Create][az-container-create] . Er is geen start opdracht regel opgegeven, dus de standaard container opdracht wordt uitgevoerd. Ter illustratie stelt dit voor beeld [omgevings variabelen](container-instances-environment-variables.md) in om de eerste drie woorden te vinden die ten minste vijf letters lang zijn:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Zodra de status van de container wordt weer gegeven als *beëindigd* (gebruik [AZ container show][az-container-show] om state te controleren), geeft u het logboek met [AZ container logs][az-container-logs] weer om de uitvoer te bekijken.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Uitvoer:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Stel nu een tweede voorbeeld container in om andere tekst te analyseren door een andere opdracht regel op te geven. Het python-script dat wordt uitgevoerd door de container, *WordCount.py*, accepteert een URL als argument en verwerkt de inhoud van die pagina in plaats van de standaard waarde.

Als u bijvoorbeeld de eerste drie woorden wilt bepalen die ten minste vijf letters lang zijn in *Romeo en Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Zodra de container is *beëindigd*, bekijkt u de uitvoer door de logboeken van de container weer te geven:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Uitvoer:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Volgende stappen

Op taak gebaseerde scenario's, zoals batch verwerking van een grote gegevensset met verschillende containers, kunnen profiteren van aangepaste opdracht regels tijdens runtime. Zie voor meer informatie over het uitvoeren van taak containers [container taken uitvoeren met beleids regels voor opnieuw opstarten](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
