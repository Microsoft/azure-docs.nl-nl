---
title: Logboeken van container-exemplaren & gebeurtenissen
description: Meer informatie over het ophalen van containerlogboeken en -gebeurtenissen in Azure Container Instances om containerproblemen op te lossen
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f5eb8c878164846ed2f1daf1cb7e5014e0c62c55
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764041"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Containerlogboeken en gebeurtenissen ophalen in Azure Container Instances

Als u een container in Azure Container Instances hebt die zich niet goed gedraagt, bekijkt u eerst de logboeken met [az container logs][az-container-logs]en streamt u de standaardfout out en standard met az container [attach][az-container-attach]. U kunt ook logboeken en gebeurtenissen voor container instances weergeven in de Azure Portal of logboek- en gebeurtenisgegevens voor containergroepen verzenden naar [Azure Monitor logboeken.](container-instances-log-analytics.md)

## <a name="view-logs"></a>Logboeken weergeven

Als u logboeken van uw toepassingscode in een container wilt weergeven, kunt u de [opdracht az container logs][az-container-logs] gebruiken.

Hieronder volgt logboekuitvoer van de voorbeeldcontainer op basis van een taak in De opdrachtregel instellen in een [containerin](container-instances-start-command.md#azure-cli-example)instance nadat u een ongeldige URL hebt opgegeven met behulp van een opdrachtregel overschrijven:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Uitvoerstromen koppelen

De [opdracht az container attach biedt][az-container-attach] diagnostische informatie tijdens het opstarten van de container. Zodra de container is gestart, worden STDOUT en STDERR naar uw lokale console gestreamd.

Hier is bijvoorbeeld uitvoer van de op taken gebaseerde container in De opdrachtregel instellen in een [container-instantie](container-instances-start-command.md#azure-cli-example)nadat u een geldige URL hebt opgegeven van een groot tekstbestand dat moet worden verwerkt:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Diagnostische gebeurtenissen op halen

Als de implementatie van uw container mislukt, controleert u de diagnostische gegevens die zijn verstrekt door de Azure Container Instances resourceprovider. Voer de opdracht az container [show][az-container-show] uit om de gebeurtenissen voor uw container weer te geven:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

De uitvoer bevat de kerneigenschappen van uw container, samen met implementatiegebeurtenissen (hier ingekort weergegeven):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [oplossen van veelvoorkomende container- en](container-instances-troubleshooting.md) implementatieproblemen voor Azure Container Instances.

Meer informatie over het verzenden van logboek- en gebeurtenisgegevens voor containergroepen [naar Azure Monitor logboeken.](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
