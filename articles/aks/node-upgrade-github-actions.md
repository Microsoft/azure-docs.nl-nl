---
title: AKS-knooppunt upgrades met GitHub-acties verwerken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het bijwerken van AKS-knoop punten met behulp van GitHub-acties
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 7a24911fd771663c7edbbdf0c8d2d763a74fc586
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96535188"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Beveiligings updates automatisch Toep assen op knoop punten van Azure Kubernetes service (AKS) met behulp van GitHub-acties

Beveiligings updates zijn een belang rijk onderdeel van het onderhouden van de beveiliging van uw AKS-cluster en het voldoen aan de meest recente oplossingen voor het onderliggende besturings systeem. Deze updates omvatten beveiligings correcties voor het besturings systeem of kernel-updates. Voor sommige updates moet het knoop punt opnieuw worden opgestart om het proces te volt ooien.

Met `az aks upgrade` kunt u een tijdige downtime van updates Toep assen. Met de opdracht worden de meest recente updates toegepast op alle knoop punten van uw cluster, wordt het verkeer naar de knoop punten cordoning en verwerkt, en worden de knoop punten opnieuw gestart. vervolgens wordt het verkeer naar de bijgewerkte knoop punten toegestaan. Als u uw knoop punten bijwerkt met een andere methode, worden uw knoop punten niet automatisch opnieuw opgestart met AKS.

> [!NOTE]
> Het belangrijkste verschil tussen het `az aks upgrade` gebruik van de `--node-image-only` vlag is dat, wanneer het wordt gebruikt, alleen de knooppunt installatie kopieën worden bijgewerkt. Als u dit weglaat, wordt de installatie kopie van het knoop punt en de versie van het Kubernetes-besturings vlak bijgewerkt. U kunt [de documenten voor beheerde upgrades op knoop punten][managed-node-upgrades-article] en [de documenten voor cluster upgrades][cluster-upgrades-article] controleren voor meer gedetailleerde informatie.

Alle knoop punten van Kubernetes worden uitgevoerd in een standaard-VM (virtuele machine) van Azure. Deze Vm's kunnen worden gebaseerd op Windows of Linux. Op Linux gebaseerde Vm's wordt een Ubuntu-installatie kopie gebruikt, waarbij het besturings systeem zo is geconfigureerd dat elke nacht automatisch wordt gecontroleerd op updates.

Wanneer u de `az aks upgrade` opdracht gebruikt, maakt Azure cli een toename van de nieuwe knoop punten met de nieuwste beveiligings-en kernel-updates. deze knoop punten zijn aanvankelijk afgebakend om te voor komen dat apps worden gepland totdat de update is voltooid. Na het volt ooien van Azure cordons (maakt het knoop punt niet meer beschikbaar voor planning van nieuwe werk belastingen) en afvoer (verplaatst de bestaande werk belastingen naar een ander knoop punt) de oudere knoop punten en uncordon de nieuwe, en worden alle geplande toepassingen naar de nieuwe knoop punten overgebracht.

Dit proces is beter dan het hand matig bijwerken van de Linux-kernels omdat Linux opnieuw moet worden opgestart wanneer een nieuwe kernel-update wordt geïnstalleerd. Als u het besturings systeem hand matig bijwerkt, moet u ook de virtuele machine opnieuw opstarten en alle apps hand matig cordoning en vervolledigen.

Dit artikel laat u zien hoe u het update proces van AKS-knoop punten kunt automatiseren. U gebruikt GitHub-acties en Azure CLI om een update taak te maken op basis van `cron` die automatisch wordt uitgevoerd.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

In dit artikel wordt ook verondersteld dat u een [github][github] -account hebt om uw acties in te maken.

## <a name="create-a-timed-github-action"></a>Een getimede GitHub-actie maken

`cron` is een hulp programma waarmee u een set opdrachten, of taak, kunt uitvoeren volgens een geautomatiseerd schema. Als u een taak wilt maken om uw AKS-knoop punten bij te werken volgens een geautomatiseerd schema, hebt u een opslag plaats nodig om uw acties te hosten. Normaal gesp roken worden GitHub-acties geconfigureerd in dezelfde opslag plaats als uw toepassing, maar u kunt elke opslag plaats gebruiken. Voor dit artikel gebruiken we uw [profiel opslagplaats][profile-repository]. Als u er nog geen hebt, maakt u een nieuwe opslag plaats met dezelfde naam als uw GitHub-gebruikers naam.

1. Navigeer naar uw opslag plaats op GitHub
1. Klik op het tabblad **acties** boven aan de pagina.
1. Als u al een werk stroom in deze opslag plaats hebt ingesteld, wordt u omgeleid naar de lijst met voltooide uitvoeringen. Klik in dit geval op de knop **nieuwe werk stroom** . Als dit uw eerste werk stroom in de opslag plaats is, presenteert GitHub u een aantal Project sjablonen, klikt u op de koppeling **een werk stroom instellen zelf** onder de tekst van de beschrijving.
1. Wijzig de werk stroom `name` en de `on` Tags die vergelijkbaar zijn met die hieronder. GitHub-acties gebruiken dezelfde [POSIX cron-syntaxis][cron-syntax] als elk Linux-systeem. In dit schema wordt aangegeven dat de werk stroom om de 15 dagen wordt uitgevoerd op 3am.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Maak een nieuwe taak aan de hand van de onderstaande. Deze taak heeft `upgrade-node` de naam, wordt uitgevoerd op een Ubuntu-agent en maakt verbinding met uw Azure cli-account om de benodigde stappen voor het upgraden van de knoop punten uit te voeren.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>De Azure CLI instellen in de werk stroom

In de `steps` sleutel definieert u alle werk dat door de werk stroom wordt uitgevoerd om de knoop punten bij te werken.

Down load en meld u aan bij de Azure CLI.

1. Ga aan de rechter kant van het scherm GitHub acties naar de *Zoek balk voor Marketplace* en typ **' Azure login '**.
1. U krijgt dan een actie met de naam Azure- **aanmelding** gepubliceerd **door Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Zoek resultaten met twee regels, de eerste actie heet ' Azure aanmelding en de tweede ' Azure Container Registry aanmelding":::

1. Klik op **Azure-aanmelding**. Klik in het volgende scherm rechtsboven in het code voorbeeld op het **pictogram kopiëren** .

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Het resultaat venster van de Azure-aanmeldings actie met code voorbeeld hieronder, rood vier kant rond een Kopieer pictogram markeert de klik spot":::

1. Plak het volgende onder de `steps` sleutel:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Voer in de Azure CLI de volgende opdracht uit om een nieuwe gebruikers naam en een nieuw wacht woord te genereren.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    De uitvoer moet vergelijkbaar zijn met de volgende JSON:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Ga **in een nieuw browser venster** naar uw github-opslag plaats en open het tabblad **instellingen** van de opslag plaats. Klik op **geheimen** en klik vervolgens op **Nieuw opslagplaats geheim**.
1. Gebruik voor *naam* `AZURE_CREDENTIALS` .
1.  Voor *waarde* voegt u de volledige inhoud toe uit de uitvoer van de vorige stap, waarbij u een nieuwe gebruikers naam en een nieuw wacht woord hebt gemaakt.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formulier met AZURE_CREDENTIALS als geheime titel en de uitvoer van de uitgevoerde opdracht geplakt als JSON":::

1. Klik op **geheim toevoegen**.

De CLI die wordt gebruikt door uw actie, wordt geregistreerd bij uw Azure-account en kan opdrachten worden uitgevoerd.

Om de stappen te maken voor het uitvoeren van Azure CLI-opdrachten.

1. Ga naar de **Zoek pagina** op *github Marketplace* aan de rechter kant van het scherm en zoek in de *Azure cli-actie*. Kies *Azure cli-actie door Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Zoek resultaat voor de actie ' Azure CLI ' waarbij het eerste resultaat wordt weer gegeven als gemaakt door Azure":::

1. Klik op de knop kopiëren in het *github Marketplace* en plak de inhoud van de actie in de hoofd editor, onder de *aanmeldings* stap van Azure, die er ongeveer als volgt uitziet:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > U kunt de `-g` `-n` para meters en uit de opdracht ontkoppelen door ze toe te voegen aan geheimen die vergelijkbaar zijn met de vorige stappen. Vervang de `{resourceGroupName}` en `{aksClusterName}` tijdelijke aanduidingen door hun geheime tegen Hangers, bijvoorbeeld `${{secrets.RESOURCE_GROUP_NAME}}``${{secrets.AKS_CLUSTER_NAME}}`

1. Geef het bestand de naam `upgrade-node-images`.
1. Klik op **door voeren starten**, voeg een bericht titel toe en sla de werk stroom op.

Zodra u de door Voer hebt gemaakt, wordt de werk stroom opgeslagen en gereed voor uitvoering.

> [!NOTE]
> Als u een groep met één knoop punt wilt bijwerken in plaats van alle knooppunt groepen op het cluster, voegt u de `--name` para meter toe aan de `az aks nodepool upgrade` opdracht om de naam van de knooppunt groep op te geven. Bijvoorbeeld:
> ```
> inlineScript: az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>De actie GitHub hand matig uitvoeren

U kunt de werk stroom hand matig uitvoeren, naast de geplande uitvoering, door een nieuwe trigger met de naam toe te voegen `on` `workflow_dispatch` . Het voltooide bestand moet er als volgt uitzien:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [opmerkingen](https://github.com/Azure/AKS/releases) bij de release van AKS voor informatie over de nieuwste knooppunt installatie kopieën.
- Meer informatie over het bijwerken van de Kubernetes-versie met [een upgrade voor een AKS-cluster][cluster-upgrades-article].
- Meer informatie over meerdere knooppunt Pools en het upgraden van knooppunt Pools met het [maken en beheren van meerdere knooppunt groepen][use-multiple-node-pools].
- Meer informatie over [systeem knooppunt groepen][system-pools]
- Zie [een steun knooppunt groep toevoegen aan AKS][spot-pools] voor meer informatie over het besparen van kosten met behulp van steun exemplaren.

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
