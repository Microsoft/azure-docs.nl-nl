---
title: Logboeken van container instanties & gebeurtenissen ophalen
description: Meer informatie over het ophalen van container logboeken en gebeurtenissen in Azure Container Instances voor het oplossen van container problemen
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746968"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Containerlogboeken en gebeurtenissen ophalen in Azure Container Instances

Wanneer u een niet-gestarte container in Azure Container Instances hebt, moet u eerst de logboeken weer geven met [AZ container logs][az-container-logs], en de standaard-out en de standaard fout stroomen met [AZ container attach][az-container-attach]. U kunt ook logboeken en gebeurtenissen voor container instanties in het Azure Portal weer geven of logboek-en gebeurtenis gegevens voor container groepen naar [Azure monitor-logboeken](container-instances-log-analytics.md)verzenden.

## <a name="view-logs"></a>Logboeken weergeven

Als u logboeken wilt weer geven vanuit de code van uw toepassing in een container, kunt u de opdracht [AZ container logs][az-container-logs] gebruiken.

Hieronder volgt een logboek uitvoer van de voor beeld-op taak gebaseerde container in [de opdracht regel in een container exemplaar instellen](container-instances-start-command.md#azure-cli-example), nadat een ongeldige URL is opgegeven met behulp van een opdracht regel onderdrukking:

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

De opdracht [AZ container attach][az-container-attach] bevat diagnostische gegevens tijdens het opstarten van de container. Zodra de container is gestart, worden STDOUT en STDERR naar uw lokale console gestreamd.

Hier ziet u bijvoorbeeld de uitvoer van de container op basis van een taak in [de opdracht regel in een container exemplaar instellen](container-instances-start-command.md#azure-cli-example), nadat u een geldige URL hebt opgegeven van een groot tekst bestand dat moet worden verwerkt:

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

## <a name="get-diagnostic-events"></a>Diagnostische gebeurtenissen ophalen

Als uw container niet kan worden geïmplementeerd, controleert u de diagnostische gegevens die door de Azure Container Instances resource provider zijn verstrekt. Als u de gebeurtenissen voor uw container wilt weer geven, voert u de opdracht [AZ container show][az-container-show] uit:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

De uitvoer bevat de kern eigenschappen van uw container, samen met de implementatie gebeurtenissen (die hier zijn afgekapt):

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
Meer informatie over het [oplossen van veelvoorkomende problemen met betrekking tot containers en implementaties](container-instances-troubleshooting.md) voor Azure container instances.

Informatie over het verzenden van logboek-en gebeurtenis gegevens voor container groepen naar [Azure monitor-logboeken](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
