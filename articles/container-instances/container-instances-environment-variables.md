---
title: Omgevingsvariabelen instellen in container-instantie
description: Meer informatie over het instellen van omgevingsvariabelen in de containers die u in Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 9d95ee3d64460aa5e11f450c9e582cdc0de4f0ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790793"
---
# <a name="set-environment-variables-in-container-instances"></a>Omgevingsvariabelen instellen in container-exemplaren

Door omgevingsvariabelen in uw containerinstanties in te stellen, kunt u dynamische configuratie mogelijk maken van de toepassing die of het script dat door de container wordt uitgevoerd. Dit is vergelijkbaar met het `--env` opdrachtregelargument voor `docker run` . 

Als u omgevingsvariabelen in een container wilt instellen, geeft u deze op wanneer u een container-instantie maakt. Dit artikel bevat voorbeelden van het instellen van omgevingsvariabelen wanneer u een container start met de [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)en [de Azure Portal](#azure-portal-example). 

Als u bijvoorbeeld de containerafbeelding [aci-wordcount][aci-wordcount] van Microsoft gebruikt, kunt u het gedrag ervan wijzigen door de volgende omgevingsvariabelen op te geven:

*NumWords:* het aantal woorden dat naar STDOUT wordt verzonden.

*MinLength:* het minimale aantal tekens in een woord dat moet worden geteld. Een hoger getal negeert veelvoorkomende woorden zoals 'van' en 'de'.

Als u geheimen wilt doorgeven als omgevingsvariabelen, Azure Container Instances beveiligde waarden [voor](#secure-values) Zowel Windows- als Linux-containers.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-voorbeeld

Als u de standaarduitvoer van de container [aci-wordcount][aci-wordcount] wilt zien, moet u deze eerst uitvoeren met deze [opdracht az container create][az-container-create] (geen omgevingsvariabelen opgegeven):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Als u de uitvoer wilt wijzigen, start u een tweede container met het toegevoegde argument, waarin u waarden opgeeft voor de `--environment-variables` *variabelen NumWords* en *MinLength.* (In dit voorbeeld wordt ervan uitgenomen dat u de CLI in een Bash-shell of een Azure Cloud Shell. Als u de Windows-opdrachtprompt gebruikt, geeft u de variabelen met dubbele aanhalingstekens op, zoals `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Zodra de status van beide containers wordt weergegeven als *Beëindigd* (gebruik [az container show om][az-container-show] de status te controleren), geeft u de logboeken weer met az container [logs][az-container-logs] om de uitvoer te bekijken.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

De uitvoer van de containers laat zien hoe u het scriptgedrag van de tweede container hebt gewijzigd door omgevingsvariabelen in te stellen.

**mycontainer1**
```output
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

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell voorbeeld

Het instellen van omgevingsvariabelen in PowerShell is vergelijkbaar met de CLI, maar maakt gebruik van het `-EnvironmentVariable` opdrachtregelargument.

Start eerst de [container aci-wordcount][aci-wordcount] in de standaardconfiguratie met deze [opdracht New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Voer nu de volgende [Opdracht New-AzContainerGroup][new-Azcontainergroup] uit. Hiermee worden de *omgevingsvariabelen NumWords* en *MinLength* opgegeven nadat een matrixvariabele is ingevuld, `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Zodra de status van beide containers *Beëindigd* is (gebruik [Get-AzContainerInstanceLog][azure-instance-log] om de status te controleren), haalt u de logboeken op met de opdracht [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

De uitvoer voor elke container laat zien hoe u het script dat door de container wordt uitgevoerd, hebt gewijzigd door omgevingsvariabelen in te stellen.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal voorbeeld

Als u omgevingsvariabelen wilt instellen wanneer u een container  in de Azure Portal, geeft u deze op de pagina Geavanceerd op wanneer u de container maakt.

1. Stel op **de pagina** Geavanceerd het beleid Voor opnieuw **opstarten in** op Bij *fout*
2. Voer **onder Omgevingsvariabelen** in met een waarde van voor de eerste variabele en voer in met een `NumWords` waarde van voor de tweede `5` `MinLength` `8` variabele. 
1. Selecteer **Beoordelen en maken om** de container te controleren en vervolgens te implementeren.

![Portalpagina met omgevingsvariabele Knop en tekstvakken inschakelen][portal-env-vars-01]

Als u de logboeken van de container wilt weergeven, **selecteert u** **onder Instellingen de optie Containers** en vervolgens **Logboeken.** Net als bij de uitvoer in de vorige cli- en PowerShell-secties, kunt u zien hoe het gedrag van het script is gewijzigd door de omgevingsvariabelen. Er worden slechts vijf woorden weergegeven, elk met een minimale lengte van acht tekens.

![Portal met uitvoer van containerlogboek][portal-env-vars-02]

## <a name="secure-values"></a>Waarden beveiligen

Objecten met beveiligde waarden zijn bedoeld voor het opslaan van gevoelige informatie, zoals wachtwoorden of sleutels voor uw toepassing. Het gebruik van beveiligde waarden voor omgevingsvariabelen is veiliger en flexibeler dan het in de containerafbeelding op te geven. Een andere optie is om geheime volumes te gebruiken, zoals beschreven in [Een geheim volume in Azure Container Instances.](container-instances-volume-secret.md)

Omgevingsvariabelen met beveiligde waarden zijn niet zichtbaar in de eigenschappen van uw container. Hun waarden zijn alleen toegankelijk vanuit de container. Containereigenschappen die worden weergegeven in de Azure Portal of Azure CLI geven bijvoorbeeld alleen de naam van een beveiligde variabele weer, niet de waarde ervan.

Stel een beveiligde omgevingsvariabele in door de eigenschap `secureValue` op te geven in plaats van de reguliere voor het type van de `value` variabele. De twee variabelen die in de volgende YAML zijn gedefinieerd, demonstreren de twee variabeletypen.

### <a name="yaml-deployment"></a>YAML-implementatie

Maak een `secure-env.yaml` bestand met het volgende codefragment.

```yaml
apiVersion: 2019-12-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer de volgende opdracht uit om de containergroep met YAML te implementeren (pas de naam van de resourcegroep indien nodig aan):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Omgevingsvariabelen controleren

Voer de [opdracht az container show uit][az-container-show] om een query uit te voeren op de omgevingsvariabelen van uw container:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Het JSON-antwoord toont zowel de sleutel als de waarde van de onveilige omgevingsvariabele, maar alleen de naam van de beveiligde omgevingsvariabele:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Met de [opdracht az container exec,][az-container-exec] waarmee een opdracht kan worden uitgevoerd in een container die wordt uitgevoerd, kunt u controleren of de beveiligde omgevingsvariabele is ingesteld. Voer de volgende opdracht uit om een interactieve bash-sessie in de container te starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Zodra u een interactieve shell in de container hebt geopend, hebt u toegang tot de waarde `SECRET` van de variabele:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Volgende stappen

Op taken gebaseerde scenario's, zoals batchverwerking van een grote gegevensset met verschillende containers, kunnen profiteren van aangepaste omgevingsvariabelen tijdens runtime. Zie Taken in containers uitvoeren met beleid voor opnieuw opstarten voor meer informatie over het uitvoeren van op taken gebaseerde [containers.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
