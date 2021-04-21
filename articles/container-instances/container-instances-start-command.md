---
title: Entrypoint in containerin instance overschrijven
description: Stel een opdrachtregel in om het toegangspunt in een containerafbeelding te overschrijven wanneer u een Azure-containerin exemplaar implementeert
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771079"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Stel de opdrachtregel in een containerin instance in om de standaardopdrachtregelbewerking te overschrijven

Wanneer u een containerinstructie maakt, kunt u eventueel een opdracht opgeven om de standaardopdrachtregelinstructie in de containerafbeelding te overschrijven. Dit gedrag is vergelijkbaar met het `--entrypoint` opdrachtregelargument voor `docker run` .

Net als bij het instellen van [omgevingsvariabelen](container-instances-environment-variables.md) voor container-exemplaren is het opgeven van een beginopdrachtregel handig voor batchtaken waarbij u elke container dynamisch moet voorbereiden met taakspecifieke configuratie.

## <a name="command-line-guidelines"></a>Richtlijnen voor de opdrachtregel

* De opdrachtregel geeft standaard één proces *aan dat wordt gestart zonder een shell* in de container. De opdrachtregel kan bijvoorbeeld een Python-script of uitvoerbaar bestand uitvoeren. Het proces kan aanvullende parameters of argumenten opgeven.

* Als u meerdere opdrachten wilt uitvoeren, start u de opdrachtregel door een shell-omgeving in te stellen die wordt ondersteund in het containerbesturingssysteem. Voorbeelden:

  |Besturingssysteem  |Standaardshell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Volg de conventies van de shell om meerdere opdrachten opeenvolgend uit te voeren.

* Afhankelijk van de containerconfiguratie moet u mogelijk een volledig pad instellen naar het uitvoerbare opdrachtregelbestand of de argumenten.

* Stel een geschikt [beleid voor opnieuw](container-instances-restart-policy.md) opstarten in voor de container-instantie, afhankelijk van of de opdrachtregel een langlopende taak of een run-once-taak specificeert. Een beleid voor opnieuw opstarten van of `Never` wordt bijvoorbeeld aanbevolen voor een taak die één keer wordt `OnFailure` uitgevoerd. 

* Als u informatie nodig hebt over het standaardinvoerpunt dat is ingesteld in een containerafbeelding, gebruikt u de [opdracht docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Opdrachtregelsyntaxis

De syntaxis van de opdrachtregel is afhankelijk van de Azure API of het hulpprogramma dat wordt gebruikt om de exemplaren te maken. Als u een shell-omgeving opgeeft, moet u ook de opdrachtsyntaxisconventie van de shell observeren.

* [az container create][az-container-create] command: Geef een tekenreeks door met de `--command-line` parameter . Voorbeeld: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: Geef een tekenreeks door met de `-Command` parameter . Bijvoorbeeld: `-Command "echo hello"`.

* Azure Portal: Geef in de eigenschap **Command override** van de containerconfiguratie een door komma's gescheiden lijst met tekenreeksen op, zonder aanhalingstekens. Voorbeeld: `python, myscript.py, arg1, arg2` ). 

* Resource Manager- of YAML-bestand, of een van de Azure SDK's: geef de opdrachtregel-eigenschap op als een matrix met tekenreeksen. Voorbeeld: de JSON-matrix `["python", "myscript.py", "arg1", "arg2"]` in een Resource Manager sjabloon. 

  Als u bekend bent met [de dockerfile-syntaxis,](https://docs.docker.com/engine/reference/builder/) is deze indeling vergelijkbaar met de *exec-vorm* van de CMD-instructie.

### <a name="examples"></a>Voorbeelden

|    |  Azure CLI   | Portal | Sjabloon | 
| ---- | ---- | --- | --- |
| **Eén opdracht** | `--command-line "python myscript.py arg1 arg2"` | **Opdracht overschrijven:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Meerdere opdrachten** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Opdracht overschrijven:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-voorbeeld

Als voorbeeld wijzigt u het gedrag van de containerafbeelding [microsoft/aci-wordcount,][aci-wordcount] die tekst analyseert in De *Graaf* van Shakespeare om de meest voorkomende woorden te vinden. In plaats van *Te analyseren,* kunt u een opdrachtregel instellen die naar een andere tekstbron wijst.

Als u de uitvoer van de container [microsoft/aci-wordcount][aci-wordcount] wilt zien wanneer de standaardtekst wordt geanalyseerd, moet u deze uitvoeren met de [volgende az container create-opdracht.][az-container-create] Er is geen startopdrachtregel opgegeven, dus de standaardcontaineropdracht wordt uitgevoerd. Ter illustratie stelt dit voorbeeld [omgevingsvariabelen](container-instances-environment-variables.md) in om de drie beste woorden te vinden die ten minste vijf letters lang zijn:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Zodra de status van de container wordt weergegeven als *Beëindigd* (gebruik [az container show om][az-container-show] de status te controleren), geeft u het logboek weer met az container [logs][az-container-logs] om de uitvoer te zien.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Uitvoer:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Stel nu een tweede voorbeeldcontainer in om verschillende tekst te analyseren door een andere opdrachtregel op te geven. Het Python-script dat wordt uitgevoerd door de container, *wordcount.py,* accepteert een URL als argument en verwerkt de inhoud van die pagina in plaats van de standaardwaarde.

Als u bijvoorbeeld de drie beste woorden wilt bepalen die ten minste vijf letters lang zijn in *DeEnt:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Zodra de container beëindigd is, *bekijkt* u de uitvoer door de logboeken van de container weer te geven:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Uitvoer:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met verschillende containers, kunnen profiteren van aangepaste opdrachtregels tijdens runtime. Zie Taken in containers uitvoeren met beleid voor opnieuw opstarten voor meer informatie over het uitvoeren van op taken gebaseerde [containers.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
