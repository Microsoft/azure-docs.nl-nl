---
title: Git-integratie voor Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Leer hoe Azure Machine Learning integreert met een lokale Git-opslagplaats om opslagplaats-, vertakkings- en huidige commit-informatie bij te houden als onderdeel van een trainingsrun.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874545"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integratie voor Azure Machine Learning

[Git](https://git-scm.com/) is een populair versiebeheersysteem waarmee u uw projecten kunt delen en er samen aan kunt werken. 

Azure Machine Learning biedt volledige ondersteuning voor Git-opslagplaatsen voor het bijhouden van werk: u kunt opslagplaatsen rechtstreeks klonen naar uw gedeelde werkruimtebestandssysteem, Git gebruiken op uw lokale werkstation of Git gebruiken vanuit een CI/CD-pijplijn.

Als bij het verzenden van een Azure Machine Learning bronbestanden worden opgeslagen in een lokale Git-opslagplaats, wordt informatie over de opslagplaats bij te houden als onderdeel van het trainingsproces.

Omdat Azure Machine Learning informatie bij houdt uit een lokale Git-opslagplaats, is deze niet gekoppeld aan een specifieke centrale opslagplaats. Uw opslagplaats kan worden gekloond vanuit GitHub, GitLab, Bitbucket, Azure DevOps of een andere met Git compatibele service.

> [!TIP]
> Gebruik Visual Studio Code om te communiceren met Git via een grafische gebruikersinterface. Als u verbinding wilt maken met Azure Machine Learning externe reken-instantie met behulp van Visual Studio Code, zie Verbinding maken met een [Azure Machine Learning compute-exemplaar in Visual Studio Code (preview)](how-to-set-up-vs-code-remote.md)
>
> Zie Visual Studio Using Version Control in VS Code (Versiebeheer gebruiken in VS Code) en Working with GitHub in VS Code (Versiebeheer gebruiken in VS Code) en [Working with GitHub in VS Code (Versiebeheer](https://code.visualstudio.com/docs/editor/github)gebruiken in VS [Code)](https://code.visualstudio.com/docs/editor/versioncontrol) voor meer informatie over de functies voor codeversiebeheer.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-opslagplaatsen klonen in het bestandssysteem van de werkruimte
Azure Machine Learning biedt een gedeeld bestandssysteem voor alle gebruikers in de werkruimte.
Als u een Git-opslagplaats wilt klonen in deze bestands share, raden we u aan een reken-exemplaar te maken & [terminal te openen.](how-to-access-terminal.md)
Zodra de terminal is geopend, hebt u toegang tot een volledige Git-client en kunt u Git klonen en ervaart met Git via de Git CLI-ervaring.

We raden u aan om de opslagplaats te klonen in uw gebruikersmap, zodat anderen niet rechtstreeks in uw werkbranche botsen.

U kunt elke Git-opslagplaats klonen die u kunt verifiëren (GitHub, Azure-opslagplaatsen, BitBucket, enzovoort)

Zie de handleiding over het gebruik van Git CLI voor meer informatie [over klonen.](https://guides.github.com/introduction/git-handbook/)

## <a name="authenticate-your-git-account-with-ssh"></a>Uw Git-account verifiëren met SSH
### <a name="generate-a-new-ssh-key"></a>Een nieuwe SSH-sleutel genereren
1) [Open het terminalvenster](./how-to-access-terminal.md) op het tabblad Azure Machine Learning Notebook.

2) Plak de onderstaande tekst, vervang door uw e-mailadres.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Hiermee maakt u een nieuwe ssh-sleutel met behulp van het opgegeven e-mailbericht als label.

```
> Generating public/private rsa key pair.
```

3) Wanneer u wordt gevraagd om een bestand in te voeren waarin u de sleutel wilt opslaan, drukt u op Enter. Hiermee accepteert u de standaardbestandslocatie.

4) Controleer of de standaardlocatie '/home/azureuser/.ssh' is en druk op Enter. Geef anders de locatie '/home/azureuser/.ssh' op.

> [!TIP]
> Zorg ervoor dat de SSH-sleutel is opgeslagen in '/home/azureuser/.ssh'. Dit bestand wordt opgeslagen op het reken-exemplaar en is alleen toegankelijk voor de eigenaar van de reken-instantie

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Typ bij de prompt een beveiligde wachtwoordzin. U wordt aangeraden een wachtwoordzin toe te voegen aan uw SSH-sleutel voor extra beveiliging

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>De openbare sleutel toevoegen aan een Git-account
1) Kopieer in het terminalvenster de inhoud van uw openbare-sleutelbestand. Als u de naam van de sleutel hebt gewijzigd, vervangt id_rsa.pub door de bestandsnaam van de openbare sleutel.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiëren en plakken in terminal**
> * Windows: `Ctrl-Insert` om te kopiëren en te gebruiken of te `Ctrl-Shift-v` `Shift-Insert` plakken.
> * Mac OS: `Cmd-c` kopiëren en `Cmd-v` plakken.
> * FireFox/IE biedt mogelijk geen goede ondersteuning voor klembordmachtigingen.

2) Selecteer en kopieer de sleuteluitvoer naar het klembord.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Begin bij **stap 2.**

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Begin bij **stap 4.**

### <a name="clone-the-git-repository-with-ssh"></a>De Git-opslagplaats klonen met SSH

1) Kopieer de SSH Git-kloon-URL uit de Git-repo.

2) Plak de URL in de `git clone` onderstaande opdracht om de URL van uw SSH Git-repo te gebruiken. Dit ziet er als de volgende uit:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

U ziet een antwoord zoals:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH kan de SSH-vingerafdruk van de server weergeven en u vragen om dit te controleren. Controleer of de weergegeven vingerafdruk overeenkomt met een van de vingerafdrukken op de pagina Openbare SSH-sleutels.

SSH geeft deze vingerafdruk weer wanneer deze verbinding maakt met een onbekende host om u te beschermen tegen [man-in-the-middle-aanvallen.](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)) Zodra u de vingerafdruk van de host accepteert, wordt u door SSH niet opnieuw gevraagd, tenzij de vingerafdruk wordt gewijzigd.

3) Wanneer u wordt gevraagd of u verbinding wilt blijven maken, typt u `yes` . Git kloont de repo en stelt de externe bron in om verbinding te maken met SSH voor toekomstige Git-opdrachten.

## <a name="track-code-that-comes-from-git-repositories"></a>Code bijhouden die afkomstig is uit Git-opslagplaatsen

Wanneer u een trainingsrun vanuit de Python-SDK of Machine Learning CLI indient, worden de bestanden die nodig zijn om het model te trainen, geüpload naar uw werkruimte. Als de opdracht beschikbaar is in uw ontwikkelomgeving, wordt deze door het uploadproces gebruikt om te controleren of de bestanden `git` zijn opgeslagen in een Git-opslagplaats. Als dat het zo is, wordt de informatie uit uw Git-opslagplaats ook geüpload als onderdeel van de trainingsrun. Deze informatie wordt opgeslagen in de volgende eigenschappen voor de trainingsrun:

| Eigenschap | Git-opdracht die wordt gebruikt om de waarde op te halen | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | De URI van waar uw opslagplaats is gekloond. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | De URI van waar uw opslagplaats is gekloond. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking toen de run werd verzonden. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking toen de run werd verzonden. |
| `azureml.git.commit` | `git rev-parse HEAD` | De commit-hash van de code die is verzonden voor de run. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | De commit-hash van de code die is verzonden voor de run. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, als de vertakking/door commit vervuild is; anders `false` . |

Deze informatie wordt verzonden voor runs die gebruikmaken van een estimator, machine learning pijplijn of script uitvoeren.

Als uw trainingsbestanden zich niet in een Git-opslagplaats in uw ontwikkelomgeving bevinden, of als de opdracht niet beschikbaar is, wordt er geen `git` git-gerelateerde informatie bijgespoord.

> [!TIP]
> Als u wilt controleren of de Git-opdracht beschikbaar is in uw ontwikkelomgeving, opent u een shellsessie, opdrachtprompt, PowerShell of een andere opdrachtregelinterface en typt u de volgende opdracht:
>
> ```
> git --version
> ```
>
> Als u en in het pad hebt geïnstalleerd, ontvangt u een antwoord dat vergelijkbaar is met `git version 2.4.1` . Zie de Git-website voor meer informatie over het installeren van git in uw [ontwikkelomgeving.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>De vastgelegde gegevens weergeven

De git-informatie wordt opgeslagen in de eigenschappen voor een trainingsrun. U kunt deze informatie bekijken met behulp van Azure Portal, Python SDK en CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw [werkruimte in de Studio-portal.](https://ml.azure.com)
1. Selecteer __Experimenten__ en selecteer vervolgens een van uw experimenten.
1. Selecteer een van de runs in de __kolom RUN NUMBER.__
1. Selecteer __Uitvoer en logboeken__ en vouw vervolgens de __logboeken en__ __azureml-vermeldingen__ uit. Selecteer de koppeling die begint met __### \_ azure__.

De vastgelegde informatie bevat tekst die vergelijkbaar is met de volgende JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python-SDK

Na het verzenden van een trainingsrun wordt een [Run-object](/python/api/azureml-core/azureml.core.run%28class%29) geretourneerd. Het `properties` kenmerk van dit object bevat de vastgelegde git-gegevens. Met de volgende code wordt bijvoorbeeld de commit-hash opgehaald:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

De `az ml run` CLI-opdracht kan worden gebruikt om de eigenschappen van een run op te halen. De volgende opdracht retourneert bijvoorbeeld de eigenschappen voor de laatste run in het experiment met de naam `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Zie de referentiedocumentatie [voor az ml run](/cli/azure/ml/run) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Rekendoelen gebruiken voor modeltraining](how-to-set-up-training-targets.md)