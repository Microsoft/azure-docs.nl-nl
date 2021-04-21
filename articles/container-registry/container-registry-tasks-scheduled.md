---
title: 'Zelfstudie: Een ACR-taak plannen'
description: In deze zelfstudie leert u hoe u een Azure Container Registry-taak volgens een gedefinieerd schema kunt uitvoeren door een of meer timertriggers in te stellen
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780853"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Zelfstudie: Een ACR-taak uitvoeren volgens een gedefinieerd schema

In deze zelfstudie leert u hoe u een [ACR-taak volgens](container-registry-tasks-overview.md) een schema kunt uitvoeren. Plan een taak door een of meer *timertriggers in te stellen.* Timertriggers kunnen alleen worden gebruikt of in combinatie met andere taaktriggers.

In deze zelfstudie leert u meer over het plannen van taken en:

> [!div class="checklist"]
> * Een taak maken met een timertrigger
> * Timertriggers beheren

Het plannen van een taak is handig voor scenario's als de volgende:

* Voer een containerworkload uit voor geplande onderhoudsbewerkingen. Voer bijvoorbeeld een container-app uit om onnodige afbeeldingen uit het register te verwijderen.
* Voer een reeks tests uit op een productie-afbeelding tijdens de werkdag als onderdeel van uw live-sitebewaking.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Over het plannen van een taak

* **Trigger met Cron-expressie:** de timertrigger voor een taak maakt gebruik van een *Cron-expressie*. De expressie is een tekenreeks met vijf velden die de minuut, het uur, de dag, de maand en de dag van de week opgeven om de taak te activeren. Frequenties van maximaal één keer per minuut worden ondersteund.

  De expressie activeert `"0 12 * * Mon-Fri"` bijvoorbeeld een taak om 12.00 uur UTC op elke weekdag. Zie [de details](#cron-expressions) verder in dit artikel.
* **Meerdere timertriggers:** het toevoegen van meerdere timers aan een taak is toegestaan, zolang de schema's verschillen.
    * Geef meerdere timertriggers op wanneer u de taak maakt of voeg ze later toe.
    * U kunt de triggers desgewenst een naam geven voor eenvoudiger beheer, ACR-taken u standaardtriggernamen op.
    * Als timerschema's tegelijk overlappen, ACR-taken de taak op het geplande tijdstip voor elke timer.
* **Andere taaktriggers:** in een door een timer geactiveerde [](container-registry-tutorial-build-task.md) taak kunt u ook triggers inschakelen op basis van het invoeren van de broncode of updates van [basisafbeeldingen.](container-registry-tutorial-base-image-update.md) Net als bij andere ACR-taken kunt u [ook handmatig een][az-acr-task-run] geplande taak uitvoeren.

## <a name="create-a-task-with-a-timer-trigger"></a>Een taak maken met een timertrigger

### <a name="task-command"></a>Taakopdracht

Vul eerst de volgende shell-omgevingsvariabele in met een waarde die geschikt is voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u de omgevingsvariabele niet in vult, moet u elke waarde handmatig vervangen waar deze wordt weergegeven in de voorbeeldopdrachten.

[![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Wanneer u een taak maakt met de [opdracht az acr task create,][az-acr-task-create] kunt u eventueel een timertrigger toevoegen. Voeg de `--schedule` parameter toe en geef een Cron-expressie door voor de timer.

Als eenvoudig voorbeeld activeert de volgende taak het uitvoeren van de afbeelding Microsoft Container Registry dag om `hello-world` 21:00 UTC. De taak wordt uitgevoerd zonder broncodecontext.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Voer de [opdracht az acr task show][az-acr-task-show] uit om te zien of de timertrigger is geconfigureerd. De trigger voor het bijwerken van basisafbeeldingen is standaard ook ingeschakeld.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>De taak activeren

Activeer de taak handmatig met [az acr task run om][az-acr-task-run] ervoor te zorgen dat deze correct is ingesteld:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Als de container wordt uitgevoerd, is de uitvoer vergelijkbaar met de volgende. De uitvoer is verkort om de belangrijkste stappen weer te geven

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Voer na de geplande tijd de [opdracht az acr task list-runs][az-acr-task-list-runs] uit om te controleren of de timer de taak heeft geactiveerd zoals verwacht:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Wanneer de timer is geslaagd, is de uitvoer vergelijkbaar met de volgende:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Timertriggers beheren

Gebruik de [opdrachten az acr task timer om][az-acr-task-timer] de timertriggers voor een ACR-taak te beheren.

### <a name="add-or-update-a-timer-trigger"></a>Een timertrigger toevoegen of bijwerken

Nadat een taak is gemaakt, kunt u eventueel een timertrigger toevoegen met behulp van [de opdracht az acr task timer add.][az-acr-task-timer-add] In het volgende voorbeeld wordt de naam timer2 van een *timertrigger toegevoegd* *aan de timertask die* u eerder hebt gemaakt. Deze timer activeert de taak elke dag om 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Werk het schema van een bestaande trigger bij of wijzig de status ervan met behulp van de [opdracht az acr task timer update.][az-acr-task-timer-update] Werk bijvoorbeeld de trigger met de naam *timer2 bij* om de taak om 11:30 UTC te activeren:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Timertriggers op een lijst zetten

De [opdracht az acr task timer list][az-acr-task-timer-list] toont de timertriggers die zijn ingesteld voor een taak:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Voorbeelduitvoer:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Een timertrigger verwijderen

Gebruik de [opdracht az acr task timer remove om][az-acr-task-timer-remove] een timertrigger uit een taak te verwijderen. In het volgende voorbeeld wordt de *timer2-trigger* uit *timertask verwijderd:*

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-expressies

ACR-taken gebruikt de [NCronTab-bibliotheek om](https://github.com/atifaziz/NCrontab) Cron-expressies te interpreteren. Ondersteunde expressies in ACR-taken vijf vereiste velden gescheiden door witruimte:

`{minute} {hour} {day} {month} {day-of-week}`

De tijdzone die wordt gebruikt met de Cron-expressies is Coordinated Universal Time (UTC). Uren hebben een 24-uursnotatie.

> [!NOTE]
> ACR-taken biedt geen ondersteuning voor het `{second}` veld of `{year}` in Cron-expressies. Als u een Cron-expressie kopieert die in een ander systeem wordt gebruikt, moet u deze velden verwijderen als deze worden gebruikt.

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer deze wordt geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>`"5 * * * *"`</nobr>|elk uur om 5 minuten na het uur|
|Alle waarden ( `*` )|<nobr>`"* 5 * * *"`</nobr>|elke minuut van het uur vanaf 5:00 UTC (60 keer per dag)|
|Een bereik ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 keer per dag om 1:00, 2:00 en 3:00 UTC|
|Een set waarden ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 keer per uur, 20 minuten, 30 minuten en 40 minuten na het uur|
|Een intervalwaarde ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 keer per uur, 10 minuten, 20 minuten, en meer, na het uur

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-voorbeelden

|Voorbeeld|Wanneer deze wordt geactiveerd  |
|---------|---------|
|`"*/5 * * * *"`|eenmaal per vijf minuten|
|`"0 * * * *"`|eenmaal boven aan elk uur|
|`"0 */2 * * *"`|eenmaal per twee uur|
|`"0 9-17 * * *"`|eenmaal per uur van 9:00 tot 17:00 UTC|
|`"30 9 * * *"`|om 9:30 UTC elke dag|
|`"30 9 * * 1-5"`|om 9:30 UTC elke weekdag|
|`"30 9 * Jan Mon"`|om 9:30 UTC elke maandag in januari|

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze reeks zelfstudies hebt gemaakt, met inbegrip van het containerregister of de registers, de containerin instance, de sleutelkluis en de service-principal, geeft u de volgende opdrachten:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Container Registry taken maakt die automatisch worden geactiveerd door een timer. 

Voor een voorbeeld van het gebruik van een geplande taak voor het ops schonen van opslagplaatsen in een register, zie Automatisch ops schonen van afbeeldingen [uit een Azure-containerregister.](container-registry-auto-purge.md)

Zie andere artikelen ACR-taken in de reeks zelfstudies voor voorbeelden van taken die worden geactiveerd door broncode-commits of updates van [basisafbeeldingen.](container-registry-tutorial-quick-task.md)



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
