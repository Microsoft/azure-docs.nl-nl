---
title: Git-integratie voor Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning integreert met een lokale Git-opslag plaats voor het bijhouden van opslag plaats, vertakking en huidige doorvoer informatie als onderdeel van een trainings uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 11/16/2020
ms.openlocfilehash: cc844cbd2518bb131f6902d1da3e7653951224b5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547846"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integratie voor Azure Machine Learning

[Git](https://git-scm.com/) is een populair versie beheersysteem waarmee u uw projecten kunt delen en Hiermee kunnen samen werken. 

Azure Machine Learning volledig ondersteunt Git-opslag plaatsen voor het bijhouden van werk-u kunt opslag plaatsen rechtstreeks naar het bestands systeem van de gedeelde werk ruimte klonen, Git gebruiken op uw lokale werk station of gebruikmaken van Git van een CI/CD-pijp lijn.

Bij het verzenden van een taak naar Azure Machine Learning, als bron bestanden worden opgeslagen in een lokale Git-opslag plaats, wordt informatie over de opslag plaats bijgehouden als onderdeel van het trainings proces.

Omdat Azure Machine Learning gegevens van een lokale Git-opslag plaats registreert, is deze niet gekoppeld aan een specifieke centrale opslag plaats. Uw opslag plaats kan worden gekloond van GitHub, GitLab, bitbucket, Azure DevOps of een andere Git-compatibele service.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-opslagplaatsen klonen in het bestandssysteem van de werkruimte
Azure Machine Learning biedt een gedeeld bestands systeem voor alle gebruikers in de werk ruimte.
Als u een Git-opslag plaats wilt klonen in deze bestands share, kunt u het beste een reken instantie maken & [een Terminal te openen](how-to-access-terminal.md).
Zodra de Terminal is geopend, hebt u toegang tot een volledige Git-client en kunt u Git klonen en gebruiken via de Git CLI-ervaring.

We raden u aan de opslag plaats te klonen in de map gebruikers, zodat anderen geen conflicten rechtstreeks op uw werk vertakking kunnen door voeren.

U kunt elke Git-opslag plaats klonen die u kunt verifiëren (GitHub, Azure opslag plaatsen, BitBucket, etc.)

Zie voor meer informatie over klonen de hand leiding voor het [gebruik van Git cli](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Uw Git-account verifiëren met SSH
### <a name="generate-a-new-ssh-key"></a>Een nieuwe SSH-sleutel genereren
1) [Open het Terminal venster](./how-to-access-terminal.md) op het tabblad Azure machine learning notitie blok.

2) Plak de onderstaande tekst en vervang deze in uw e-mail adres.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Hiermee maakt u een nieuwe SSH-sleutel met behulp van de meegeleverde e-mail als label.

```
> Generating public/private rsa key pair.
```

3) Wanneer u wordt gevraagd om een bestand op te geven waarin u de sleutel wilt opslaan, drukt u op ENTER. Hiermee accepteert u de standaard bestands locatie.

4) Controleer of de standaard locatie '/Home/azureuser/.ssh ' is en druk op ENTER. Geef anders de locatie '/Home/azureuser/.ssh ' op.

> [!TIP]
> Zorg ervoor dat de SSH-sleutel is opgeslagen in '/Home/azureuser/.ssh '. Dit bestand wordt opgeslagen op het reken exemplaar is alleen toegankelijk voor de eigenaar van het reken exemplaar

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Typ bij de prompt een beveiligde wachtwoordzin. U wordt aangeraden een wachtwoordzin aan uw SSH-sleutel toe te voegen voor extra beveiliging

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>De open bare sleutel toevoegen aan het git-account
1) Kopieer de inhoud van uw open bare-sleutel bestand in het Terminal venster. Als u de naam van de sleutel hebt gewijzigd, vervangt u id_rsa. pub door de bestands naam van de open bare sleutel.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiëren en plakken in Terminal**
> * Windows: `Ctrl-Insert` kopiëren en gebruiken `Ctrl-Shift-v` of `Shift-Insert` Plakken.
> * Mac OS: `Cmd-c` om te kopiëren en `Cmd-v` te plakken.
> * FireFox/IE ondersteunt mogelijk geen juiste Klembord machtigingen.

2) Selecteer en kopieer de sleutel uitvoer in het klem bord.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure-DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Begin bij **stap 2**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Begin bij **stap 4**.

### <a name="clone-the-git-repository-with-ssh"></a>De Git-opslag plaats klonen met SSH

1) Kopieer de URL van de SSH Git-kloon vanuit de Git-opslag plaats.

2) Plak de URL in de `git clone` onderstaande opdracht om de URL van uw SSH Git opslag plaats te gebruiken. Dit ziet er ongeveer als volgt uit:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Er wordt een antwoord weer geven als:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH kan de SSH-vinger afdruk van de server weer geven en u vragen om deze te verifiëren. Controleer of de weer gegeven vinger afdruk overeenkomt met een van de vinger afdrukken op de pagina open bare SSH-sleutels.

SSH geeft deze vinger afdruk weer wanneer deze verbinding maakt met een onbekende host om u te beschermen tegen [man-in-the-middle-aanvallen](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Zodra u de vinger afdruk van de host hebt geaccepteerd, wordt u niet gevraagd om u opnieuw te vragen, tenzij de vinger afdruk is gewijzigd.

3) Wanneer u wordt gevraagd of u wilt door gaan met verbinding maken, typt u `yes` . Git kloont de opslag plaats en stelt de externe oorsprong in om verbinding te maken met SSH voor toekomstige Git-opdrachten.

## <a name="track-code-that-comes-from-git-repositories"></a>Code traceren die afkomstig is van Git-opslag plaatsen

Wanneer u een training verzendt die wordt uitgevoerd vanuit de python-SDK of Machine Learning CLI, worden de bestanden die nodig zijn om het model te trainen, geüpload naar uw werk ruimte. Als de `git` opdracht beschikbaar is in uw ontwikkelings omgeving, gebruikt het upload proces om te controleren of de bestanden zijn opgeslagen in een Git-opslag plaats. Als dat het geval is, worden de gegevens uit uw Git-opslag plaats ook geüpload als onderdeel van de trainings uitvoering. Deze informatie wordt opgeslagen in de volgende eigenschappen voor het uitvoeren van de training:

| Eigenschap | Git-opdracht die wordt gebruikt om de waarde op te halen | Beschrijving |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | De URI waaruit uw opslag plaats is gekloond. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | De URI waaruit uw opslag plaats is gekloond. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `azureml.git.commit` | `git rev-parse HEAD` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`Als de vertakking/door Voer is beschadigd; anders, `false` . |

Deze informatie wordt verzonden voor uitvoeringen die gebruikmaken van een Estimator, machine learning pijp lijn of script uitvoering.

Als uw trainings bestanden zich niet in een Git-opslag plaats in uw ontwikkelings omgeving bevinden, of als de `git` opdracht niet beschikbaar is, wordt er geen informatie over git bijgehouden.

> [!TIP]
> Als u wilt controleren of de Git-opdracht beschikbaar is in uw ontwikkelings omgeving, opent u een shell-sessie, opdracht prompt, Power shell of een andere opdracht regel interface en typt u de volgende opdracht:
>
> ```
> git --version
> ```
>
> Als deze is geïnstalleerd en u in het pad een antwoord ontvangt dat vergelijkbaar is met `git version 2.4.1` . Zie de [Git-website](https://git-scm.com/)voor meer informatie over het installeren van git in uw ontwikkel omgeving.

## <a name="view-the-logged-information"></a>De geregistreerde gegevens weer geven

De Git-informatie wordt opgeslagen in de eigenschappen voor een trainings uitvoering. U kunt deze informatie weer geven met behulp van de Azure Portal, python SDK en CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw werk ruimte vanuit de [Studio Portal](https://ml.azure.com).
1. Selecteer __experimenten__ en selecteer vervolgens een van uw experimenten.
1. Selecteer een van de uitvoeringen in de kolom __uitvoerings nummer__ .
1. Selecteer __uitvoer en logboeken__ en vouw vervolgens de __Logboeken__ en __azureml__ -vermeldingen uit. Selecteer de koppeling die begint met __### \_ Azure__.

De geregistreerde gegevens bevatten tekst die vergelijkbaar is met de volgende JSON:

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

Na het verzenden van een trainings uitvoering wordt een [Run](/python/api/azureml-core/azureml.core.run%28class%29) -object geretourneerd. Het `properties` kenmerk van dit object bevat de geregistreerde Git-informatie. Met de volgende code wordt bijvoorbeeld de commit-hash opgehaald:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

De `az ml run` cli-opdracht kan worden gebruikt om de eigenschappen op te halen uit een run. De volgende opdracht retourneert bijvoorbeeld de eigenschappen voor de laatste uitvoering in het experiment met de naam `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Zie voor meer informatie de documentatie van [AZ ml run](/cli/azure/ext/azure-cli-ml/ml/run) Reference.

## <a name="next-steps"></a>Volgende stappen

* [Reken doelen voor model training gebruiken](how-to-set-up-training-targets.md)