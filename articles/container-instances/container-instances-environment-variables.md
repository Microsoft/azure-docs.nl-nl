---
title: Omgevings variabelen instellen in container exemplaar
description: Meer informatie over het instellen van omgevings variabelen in de containers die u uitvoert in Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169762"
---
# <a name="set-environment-variables-in-container-instances"></a>Omgevings variabelen instellen in container instanties

Door omgevingsvariabelen in uw containerinstanties in te stellen, kunt u dynamische configuratie mogelijk maken van de toepassing die of het script dat door de container wordt uitgevoerd. Dit is vergelijkbaar met het `--env` opdracht regel argument tot `docker run` . 

Als u omgevings variabelen in een container wilt instellen, geeft u ze op wanneer u een container exemplaar maakt. In dit artikel worden voor beelden gegeven van het instellen van omgevings variabelen wanneer u een container start met de [Azure cli](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)en de [Azure Portal](#azure-portal-example). 

Als u bijvoorbeeld de container installatie kopie van micro soft [ACI-WordCount][aci-wordcount] uitvoert, kunt u het gedrag ervan wijzigen door de volgende omgevings variabelen op te geven:

*NumWords*: het aantal woorden dat is verzonden naar stdout.

*MinLength*: het minimale aantal tekens in een woord dat moet worden geteld. Bij een hogere waarde worden veelvoorkomende woorden, zoals ' van ' en ' de ', genegeerd.

Als u geheimen als omgevings variabelen wilt door geven, ondersteunt Azure Container Instances [beveiligde waarden](#secure-values) voor zowel Windows-als Linux-containers.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Voor beeld van Azure CLI

Als u de standaard uitvoer van de [ACI-WordCount-][aci-wordcount] container wilt weer geven, voert u deze eerst uit met deze opdracht [AZ container Create][az-container-create] (er zijn geen omgevings variabelen opgegeven):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Als u de uitvoer wilt wijzigen, start u een tweede container waarbij het `--environment-variables` argument is toegevoegd, waarbij u waarden opgeeft voor de variabelen *NumWords* en *MinLength* . (In dit voor beeld wordt ervan uitgegaan dat u de CLI uitvoert in een bash-shell of Azure Cloud Shell. Als u de Windows-opdracht prompt gebruikt, geeft u de variabelen op met dubbele aanhalings tekens, zoals `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Als de status van beide containers wordt weer gegeven als *beëindigd* (gebruik [AZ container show][az-container-show] om state te controleren), geeft u de logboeken weer met [AZ container logs][az-container-logs] om de uitvoer te bekijken.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

In de uitvoer van de containers ziet u hoe u het script gedrag van de tweede container hebt gewijzigd door omgevings variabelen in te stellen.

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

## <a name="azure-powershell-example"></a>Azure PowerShell-voor beeld

Het instellen van omgevings variabelen in Power shell is vergelijkbaar met de CLI, maar gebruikt het `-EnvironmentVariable` opdracht regel argument.

Start eerst de container [ACI-WordCount][aci-wordcount] in de standaard configuratie met de opdracht [New-AzContainerGroup][new-Azcontainergroup] :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Voer nu de volgende opdracht [AzContainerGroup][new-Azcontainergroup] uit. Hiermee geeft u de variabelen *NumWords* en *MinLength* -omgeving op nadat u een matrix variabele hebt gevuld `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Zodra de status van beide containers is *beëindigd* (gebruik [Get-AzContainerInstanceLog][azure-instance-log] om de status te controleren), haalt u de logboeken op met de opdracht [Get-AzContainerInstanceLog][azure-instance-log] .

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

De uitvoer voor elke container laat zien hoe u het script dat door de container wordt uitgevoerd, heeft gewijzigd door omgevings variabelen in te stellen.

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

## <a name="azure-portal-example"></a>Azure Portal-voor beeld

Als u omgevings variabelen wilt instellen wanneer u een container in de Azure Portal start, geeft u deze op op de pagina **Geavanceerd** wanneer u de container maakt.

1. Stel op de pagina **Geavanceerd** het **beleid voor opnieuw opstarten** in *op bij fout*
2. Onder **omgevings variabelen** voert u `NumWords` een waarde in `5` voor de eerste variabele en voert u `MinLength` een waarde in `8` voor de tweede variabele. 
1. Selecteer **controleren + maken** om de container te controleren en vervolgens te implementeren.

![Portal pagina met de knop voor het inschakelen van de omgevings variabele en tekst vakken][portal-env-vars-01]

Als u de logboeken van de container wilt weer geven, selecteert u onder **instellingen** de optie **containers** en vervolgens **Logboeken**. Net als bij de uitvoer die wordt weer gegeven in de vorige CLI-en Power shell-secties, kunt u zien hoe het gedrag van het script door de omgevings variabelen is gewijzigd. Er worden slechts vijf woorden weer gegeven, elk met een minimum lengte van acht tekens.

![Portal met uitvoer van container logboek][portal-env-vars-02]

## <a name="secure-values"></a>Beveiligde waarden

Objecten met beveiligde waarden zijn bedoeld om gevoelige informatie te bewaren, zoals wacht woorden of sleutels voor uw toepassing. Het gebruik van beveiligde waarden voor omgevings variabelen is zowel veiliger als flexibeler dan het opnemen in de installatie kopie van de container. Een andere optie is het gebruik van geheime volumes, zoals beschreven in [een geheim volume koppelen in azure container instances](container-instances-volume-secret.md).

Omgevings variabelen met beveiligde waarden zijn niet zichtbaar in de eigenschappen van uw container. hun waarden kunnen alleen worden geopend vanuit de container. Container eigenschappen die in de Azure Portal of in azure CLI worden weer gegeven, geven bijvoorbeeld alleen de naam van een beveiligde variabele weer, niet de waarde.

Stel een beveiligde omgevings variabele in door de eigenschap op te geven `secureValue` in plaats van de reguliere waarde `value` voor het type variabele. De twee variabelen die in de volgende YAML zijn gedefinieerd, worden de twee typen variabelen gedemonstreerd.

### <a name="yaml-deployment"></a>YAML-implementatie

Maak een `secure-env.yaml` bestand met het volgende code fragment.

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

Voer de volgende opdracht uit om de container groep te implementeren met YAML (Wijzig indien nodig de naam van de resource groep):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Omgevings variabelen verifiëren

Voer de opdracht [AZ container show][az-container-show] uit om de omgevings variabelen van uw container op te vragen:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

In het JSON-antwoord worden de sleutel en waarde van de variabele niet-veilige omgeving weer gegeven, maar alleen de naam van de variabele beveiligde omgeving:

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

Met de opdracht [AZ container exec][az-container-exec] , waarmee u een opdracht kunt uitvoeren in een container die wordt uitgevoerd, kunt u controleren of de variabele Secure Environment is ingesteld. Voer de volgende opdracht uit om een interactieve bash-sessie in de container te starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Zodra u een interactieve shell in de container hebt geopend, hebt u toegang tot de `SECRET` waarde van de variabele:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Volgende stappen

Op taak gebaseerde scenario's, zoals batch verwerking van een grote gegevensset met verschillende containers, kunnen profiteren van aangepaste omgevings variabelen tijdens runtime. Zie voor meer informatie over het uitvoeren van taak containers [container taken uitvoeren met beleids regels voor opnieuw opstarten](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
