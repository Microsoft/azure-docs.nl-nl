---
title: Beleid voor opnieuw opstarten voor taken die één keer worden uitgevoerd
description: Leer hoe u Azure Container Instances om taken uit te voeren die worden uitgevoerd tot voltooiing, zoals in build-, test- of renderingtaken voor afbeeldingen.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 3bce208e3663ecfcebe520be92de3ac4443c0c8f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771151"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Taken in containers uitvoeren met beleid voor opnieuw opstarten

Het gemak en de snelheid waarmee containers in Azure Container Instances worden geïmplementeerd, zorgt voor een aantrekkelijk platform voor het uitvoeren van eenmalige taken zoals bouwen, testen en rendering van afbeeldingen in een containerinstantie.

Met een configureerbaar beleid voor opnieuw starten kunt u opgeven dat uw containers worden gestopt wanneer hun processen zijn voltooid. Omdat containerinstanties per seconde worden gefactureerd, betaalt u alleen voor de rekenresources die worden gebruikt terwijl de container waar uw taak wordt uitgevoerd actief is.

In de voorbeelden die in dit artikel worden gepresenteerd, wordt de Azure CLI gebruikt. U moet Azure CLI versie 2.0.21 of hoger lokaal hebben geïnstalleerd [of][azure-cli-install]de CLI in de [Azure Cloud Shell.](../cloud-shell/overview.md)

## <a name="container-restart-policy"></a>Beleid voor opnieuw opstarten van container

Wanneer u een [containergroep in Azure Container Instances,](container-instances-container-groups.md) kunt u een van de drie instellingen voor opnieuw opstarten opgeven.

| Beleid voor opnieuw starten   | Beschrijving |
| ---------------- | :---------- |
| `Always` | Containers in de containergroep worden altijd opnieuw gestart. Dit is de **standaard** instelling die wordt toegepast wanneer er geen beleid voor opnieuw starten wordt opgegeven bij het maken van een container. |
| `Never` | Containers in de containergroep worden nooit opnieuw gestart. De containers worden maximaal één keer uitgevoerd. |
| `OnFailure` | Containers in de containergroep worden alleen opnieuw gestart als het proces in de container mislukt (wanneer deze wordt afgesloten met een andere afsluitcode dan nul). De containers worden ten minste één keer uitgevoerd. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Beleid voor opnieuw opstarten opgeven

Hoe u een beleid voor opnieuw opstarten opgeeft, is afhankelijk van hoe u container-exemplaren maakt, zoals met de Azure CLI, Azure PowerShell-cmdlets of in de Azure Portal. Geef in de Azure CLI de `--restart-policy` parameter op wanneer u az container create [aanroept.][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Voorbeeld van uitvoeren naar voltooiing

Als u het beleid voor opnieuw opstarten in actie wilt zien, maakt u een container-instantie van de [Microsoft-afbeelding aci-wordcount][aci-wordcount-image] en geeft u het beleid voor `OnFailure` opnieuw opstarten op. In deze voorbeeldcontainer wordt een Python-script uitgevoerd dat standaard de tekst van Shakespeare's [Text](http://shakespeare.mit.edu/hamlet/full.html)analyseert, de tien meest voorkomende woorden naar STDOUT schrijft en vervolgens wordt afgesloten.

Voer de voorbeeldcontainer uit met de [volgende az container create-opdracht:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances start de container en stopt deze wanneer de toepassing (of het script in dit geval) wordt afgesloten. Wanneer Azure Container Instances een container stopt waarvan het beleid voor opnieuw opstarten of is, wordt de status van `Never` de container ingesteld op `OnFailure` **Beëindigd.** U kunt de status van een container controleren met de [opdracht az container show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Voorbeelduitvoer:

```bash
"Terminated"
```

Wanneer de status van de voorbeeldcontainer *Beëindigd* is, kunt u de taakuitvoer zien door de containerlogboeken te bekijken. Voer de opdracht [az container logs][az-container-logs] uit om de uitvoer van het script te bekijken:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Uitvoer:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

In dit voorbeeld ziet u de uitvoer die het script naar STDOUT heeft verzonden. Uw taken in containers kunnen echter in plaats daarvan de uitvoer naar de permanente opslag schrijven om ze later op te halen. Bijvoorbeeld naar een [Azure-bestands share](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met verschillende containers, kunnen tijdens runtime profiteren van aangepaste [omgevingsvariabelen](container-instances-environment-variables.md) of [opdrachtregels.](container-instances-start-command.md)

Zie Een Azure-bestands share toevoegen met Azure Container Instances voor meer informatie over het persistent maken van de uitvoer van uw containers die worden [uitgevoerd tot Azure Container Instances.](./container-instances-volume-azure-files.md)

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
