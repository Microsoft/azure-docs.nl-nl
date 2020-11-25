---
title: Resources versus code-uitbrei ding maken en beheren (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en beheren van Azure Machine Learning-resources met behulp van de Azure Machine Learning Visual Studio code extension.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010670"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Azure Machine Learning resources beheren met de extensie VS (preview)

Meer informatie over het beheren van Azure Machine Learning resources met de VS code-extensie.

![Azure Machine Learning VS code-uitbrei ding](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u nog geen abonnement heeft, registreert u zich of probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Als u het nog niet hebt, moet u dit eerst [installeren](https://code.visualstudio.com/docs/setup/setup-overview).
- VS code Azure Machine Learning-extensie. Volg de [installatie handleiding voor de Azure machine learning VS code-extensie](tutorial-setup-vscode-extension.md#install-the-extension) om de uitbrei ding te installeren.

In alle onderstaande processen wordt ervan uitgegaan dat u zich in de weer gave Azure Machine Learning in Visual Studio code bevindt. Als u de uitbrei ding wilt starten, selecteert u het pictogram van **Azure** in de activiteit balk VS code.

## <a name="workspaces"></a>Werkruimten

Zie [werk ruimten](concept-workspace.md)voor meer informatie.

### <a name="create-a-workspace"></a>Een werkruimte maken

1. Klik in de weer gave Azure Machine Learning met de rechter muisknop op uw abonnements knooppunt en selecteer **werk ruimte maken**.
1. In de prompt:
    1. Geef een naam op voor uw werk ruimte
    1. Kies uw Azure-abonnement
    1. Kies of maak een nieuwe resource groep om de werk ruimte in te richten
    1. Selecteer de locatie waar de werk ruimte moet worden ingericht.

Alternatieve methoden voor het maken van een werk ruimte zijn onder andere:

- Open de **weer gave opdracht palet > opdracht palet** en voer in de tekst prompt **Azure ml: werk ruimte maken** in.
- Klik op het `+` pictogram boven aan de weer gave Azure machine learning.
- Maak een nieuwe werk ruimte wanneer u wordt gevraagd om een werk ruimte te selecteren tijdens het inrichten van andere resources.

### <a name="remove-a-workspace"></a>Een werk ruimte verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Klik met de rechter muisknop op de werk ruimte die u wilt verwijderen.
1. Selecteer of u wilt verwijderen:
    - *Alleen de werk ruimte*: met deze optie wordt **alleen** de Azure-resource van de werk ruimte verwijderd. De resource groep, opslag accounts en andere resources waaraan de werk ruimte is gekoppeld, bevinden zich nog in Azure.
    - *Met gekoppelde resources: met* deze optie worden de werk ruimte **en** alle bijbehorende resources verwijderd.

## <a name="datastores"></a>Gegevensarchieven

De VS code-extensie ondersteunt momenteel gegevens opslag van de volgende typen:

- Azure-bestandsshare
- Azure Blob Storage

Wanneer u een werk ruimte maakt, wordt er een gegevens opslag voor elk van deze typen gemaakt.

Zie [data stores](concept-data.md#datastores)voor meer informatie.

### <a name="create-a-datastore"></a>Een gegevensarchief maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit waarvoor u de gegevens opslag wilt maken.
1. Klik met de rechter muisknop op het knoop punt **gegevens opslag** en selecteer **gegevens opslag registreren**.
1. In de prompt:
    1. Geef een naam op voor uw gegevens opslag.
    1. Kies het type gegevens opslag.
    1. Selecteer uw opslag resource. U kunt een opslag resource kiezen die aan uw werk ruimte is gekoppeld of een wille keurige geldige opslag resource in uw Azure-abonnementen selecteren.
    1. Kies de container waarin uw gegevens zich in de eerder geselecteerde opslag Resource bevinden.
1. Een configuratie bestand wordt weer gegeven in VS code. Als u tevreden bent met uw configuratie bestand, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="manage-a-datastore"></a>Een gegevens opslag beheren

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt **gegevens opslag** in uw werk ruimte uit.
1. Selecteer de gegevens opslag die u wilt:
    - *Als standaard instellen*. Wanneer u experimenten uitvoert, is dit de gegevens opslag die wordt gebruikt.
    - *Alleen-lezen instellingen controleren*.
    - *Modify*. Wijzig het verificatie type en de referenties. Ondersteunde verificatie typen zijn onder andere account sleutel en SAS-token.

## <a name="datasets"></a>Gegevenssets

De uitbrei ding ondersteunt momenteel de volgende typen gegevensset:

- *In tabel vorm*: Hiermee kunt u gegevens realiseren in een data frame (Pandas of PySpark).
- *Bestand*: een bestand of verzameling bestanden. Hiermee kunt u bestanden downloaden of koppelen aan uw compute.

Zie [gegevens sets](concept-data.md#datasets) voor meer informatie.

### <a name="create-dataset"></a>Gegevensset maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit waarvoor u de gegevens opslag wilt maken.
1. Klik met de rechter muisknop op het knoop punt **gegevens sets** en selecteer **gegevensset maken**.
1. In de prompt:
    1. Het type gegevensset kiezen
    1. Definiëren of de gegevens zich op uw PC of op het web bevinden
    1. Geef de locatie van uw gegevens op. Dit kan één bestand zijn of een map met uw gegevens bestanden.
    1. Kies de Data Store waarnaar u uw gegevens wilt uploaden.
    1. Geef een voor voegsel op waarmee u uw gegevensset in het gegevens archief kunt identificeren.

### <a name="version-datasets"></a>Versie gegevens sets

Wanneer u machine learning modellen bouwt, kunt u een versie van uw gegevensset maken wanneer u gegevens wijzigt. Hiertoe gaat u naar de VS code-extensie:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt **gegevens sets** uit.
1. Klik met de rechter muisknop op de gegevensset die u wilt versie en selecteer **nieuwe versie maken**.
1. In de prompt:
    1. Het type gegevensset selecteren
    1. Definiëren of de gegevens zich op uw PC of op het web bevinden.
    1. Geef de locatie van uw gegevens op. Dit kan één bestand zijn of een map met uw gegevens bestanden.
    1. Kies de Data Store waarnaar u uw gegevens wilt uploaden.
    1. Geef een voor voegsel op waarmee u uw gegevensset in het gegevens archief kunt identificeren.

### <a name="view-dataset-properties"></a>Eigenschappen van gegevensset weer geven

Met deze optie kunt u meta gegevens weer geven die zijn gekoppeld aan een specifieke gegevensset. Hiertoe gaat u naar de VS code-extensie:

1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt **gegevens sets** uit.
1. Klik met de rechter muisknop op de gegevensset die u wilt controleren en selecteer **Eigenschappen van gegevensset weer geven**. Hiermee wordt een configuratie bestand weer gegeven met de eigenschappen van de meest recente gegevensset-versie.

> [!NOTE]
> Als u meerdere versies van uw gegevensset hebt, kunt u ervoor kiezen om alleen de eigenschappen van de gegevensset van een specifieke versie weer te geven door het knoop punt gegevensset uit te vouwen en dezelfde stappen uit te voeren die in deze sectie worden beschreven op de gewenste versie.

### <a name="unregister-datasets"></a>Registratie van gegevens sets opheffen

Als u een gegevensset en alle versies ervan wilt verwijderen, moet u de registratie ervan opheffen. Hiertoe gaat u naar de VS code-extensie:

1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt **gegevens sets** uit.
1. Klik met de rechter muisknop op de gegevensset die u wilt verwijderen en selecteer de **registratie van gegevensset** opheffen.

## <a name="environments"></a>Omgevingen

Zie [omgevingen](concept-environments.md)voor meer informatie.

### <a name="create-environment"></a>Omgeving maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit waarvoor u de gegevens opslag wilt maken.
1. Klik met de rechter muisknop op het knoop punt **omgevingen** en selecteer **omgeving maken**.
1. In de prompt:
    1. Geef een naam op voor uw omgeving
    1. Definieer de configuratie van uw omgeving:
        - *Gecuratore omgevingen*: vooraf geconfigureerde omgevingen in azure machine learning. U kunt de omgeving verder aanpassen door de `dependencies` eigenschap in het JSON-bestand te wijzigen. Meer informatie over [gecuratore omgevingen](resource-curated-environments.md).
        - *Conda afhankelijkheden bestand*: voor Anaconda-omgevingen kan het bestand dat de omgevings definitie bevat, worden gegeven.
        - *PIP-vereisten bestand*: voor PIP-omgevingen kan het bestand dat uw omgevings definitie bevat, worden gegeven.
        - *Bestaande Conda-omgeving*: met deze optie zoekt u naar de Conda-omgevingen in uw lokale PC en probeert u een omgeving te bouwen vanuit de geselecteerde omgeving.
        - *Aangepast*: uw eigen kanalen en afhankelijkheden definiëren
    1. Een configuratie bestand wordt geopend in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="view-environment-configurations"></a>Omgevings configuraties weer geven

De afhankelijkheden en configuraties voor een specifieke omgeving in de uitbrei ding bekijken:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt **omgevingen** uit.
1. Klik met de rechter muisknop op de omgeving die u wilt weer geven en selecteer **omgeving weer geven**.

### <a name="edit-environment-configurations"></a>Omgevings configuraties bewerken

De afhankelijkheden en configuraties voor een specifieke omgeving in de extensie bewerken:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **omgevingen** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de omgeving die u wilt weer geven en selecteer **omgeving bewerken**.
1. Als u de wijzigingen hebt aangebracht en u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code-opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

## <a name="experiments"></a>Experimenten

Zie [experimenten](concept-azure-machine-learning-architecture.md#experiments)voor meer informatie.

### <a name="create-experiment"></a>Experiment maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Klik met de rechter muisknop op het knoop punt **experimenten** in uw werk ruimte en selecteer **experiment maken**.
1. Geef in de prompt een naam op voor uw experiment.

### <a name="run-experiment"></a>Experiment uitvoeren

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **experimenten** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het experiment dat u wilt uitvoeren.
1. Selecteer het pictogram **experiment uitvoeren** op de activiteiten balk.
1. Selecteer of u uw experiment lokaal of op afstand wilt uitvoeren. Raadpleeg de [gids voor fout opsporing](how-to-debug-visual-studio-code.md) voor meer informatie over het lokaal uitvoeren en oplossen van problemen met experimenten.
1. Kies uw abonnement.
1. Kies de Azure-ML-werkruimte om het experiment uit te voeren.
1. Kies uw experiment.
1. Kies of maak een compute om het experiment uit te voeren.
1. Kies of maak een configuratie voor het uitvoeren van uw experiment.

U kunt ook de knop **experiment uitvoeren** selecteren aan de bovenkant van de uitbrei ding en de uitvoering van uw experiment configureren in de prompt.

### <a name="view-experiment"></a>Experiment weer geven

U kunt als volgt uw experiment bekijken in Azure Machine Learning Studio:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **experimenten** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het experiment dat u wilt weer geven en selecteer **experiment weer geven**. 
1. Er wordt een prompt weer gegeven waarin u wordt gevraagd om de URL van het experiment te openen in Azure Machine Learning Studio. Selecteer **Openen**.

### <a name="track-run-progress"></a>Voortgang van uitvoering bijhouden

Wanneer u uw experiment uitvoert, wilt u mogelijk de voortgang zien. Voor het volgen van de voortgang van een uitvoering in Azure Machine Learning Studio van de extensie:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **experimenten** in uw werk ruimte uit.
1. Vouw het experiment-knoop punt uit waarvoor u de voortgang wilt bijhouden.
1. Klik met de rechter muisknop op de uitvoeren en selecteer **weer gave uitvoeren in azure Portal**.
1. Er wordt een prompt weer gegeven waarin u wordt gevraagd om de uitvoer-URL te openen in Azure Machine Learning Studio. Selecteer **Openen**.

### <a name="download-run-logs--outputs"></a>Uitvoer logboeken voor de Download & uitvoeren

Zodra een uitvoering is voltooid, kunt u de logboeken en assets, zoals het model dat is gegenereerd als onderdeel van een experiment, downloaden.

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **experimenten** in uw werk ruimte uit.
1. Vouw het experiment-knoop punt uit waarvoor u de voortgang wilt bijhouden.
1. Klik met de rechter muisknop op de run:
    - Als u de uitvoer wilt downloaden, selecteert u de optie voor het **downloaden van uitvoer**.
    - Selecteer **Logboeken downloaden** om de logboeken te downloaden.

### <a name="view-run-metadata"></a>Meta gegevens van uitvoering weer geven

In de uitbrei ding kunt u meta gegevens controleren, zoals de uitvoerings configuratie die wordt gebruikt voor de uitvoering en Details van de uitvoering.

## <a name="compute-instances"></a>Rekenprocessen

Zie [Compute instances](concept-compute-instance.md)voor meer informatie.

### <a name="create-compute-instance"></a>Reken instantie maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit waarvoor u het Compute-exemplaar wilt maken.
1. Klik met de rechter muisknop op het knoop punt **reken instanties** en selecteer **Create Compute instance**.
1. In de prompt:
    1. Geef een naam op voor het reken exemplaar.
    1. Selecteer een VM-grootte in de lijst.
    1. Kies of u SSH-toegang wilt inschakelen.
        1. Als u SSH-toegang inschakelt, moet u ook de open bare SSH-sleutel of het bestand met de sleutel opgeven. Zie de hand leiding voor het [maken en gebruiken van SSH-sleutels op Azure](../virtual-machines/linux/mac-create-ssh-keys.md)voor meer informatie.

### <a name="stop-or-restart-compute-instance"></a>Reken instantie stoppen of opnieuw starten

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken instantie** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het reken exemplaar dat u wilt stoppen of opnieuw wilt starten en selecteer **Compute instance stoppen** of **Compute instance respectievelijk opnieuw opstarten** .

### <a name="view-compute-instance-configuration"></a>Configuratie van reken instanties weer geven

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken instantie** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het reken exemplaar dat u wilt controleren en selecteer **Eigenschappen van reken instanties weer geven**.

### <a name="delete-compute-instance"></a>Reken instantie verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken instantie** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het reken exemplaar dat u wilt verwijderen en selecteer **reken instantie verwijderen**.

## <a name="compute-clusters"></a>Reken clusters

De uitbrei ding ondersteunt de volgende reken typen:

- Azure Machine Learning-rekenclusters
- Azure Kubernetes Service

Zie [Compute-doelen](concept-compute-target.md#train)voor meer informatie.

### <a name="create-compute"></a>Reken proces maken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit waarop u het berekenings cluster wilt maken.
1. Klik met de rechter muisknop op het knoop punt **reken clusters** en selecteer **Compute maken**.
1. In de prompt:
    1. Een reken type kiezen
    1. Kies een VM-grootte. Meer informatie over [VM-grootten](../virtual-machines/sizes.md).
    1. Geef een naam op voor de reken kracht.

### <a name="view-compute-configuration"></a>Compute-configuratie weer geven

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken clusters** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de compute die u wilt weer geven en selecteer **reken eigenschappen weer geven**.

### <a name="edit-compute-scale-settings"></a>Instellingen voor reken schaal bewerken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken clusters** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de compute die u wilt bewerken en selecteer **reken bewerking bewerken**.
1. Een configuratie bestand voor uw Compute wordt geopend in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="delete-compute"></a>Reken proces verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken clusters** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de compute die u wilt verwijderen en selecteer **reken proces verwijderen**.

### <a name="create-run-configuration"></a>Configuratie voor uitvoeren maken

Een uitvoerings configuratie maken in de extensie:

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **reken clusters** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het doel van de berekening waarvoor u de configuratie voor uitvoeren wilt maken en selecteer vervolgens **uitvoerings configuratie maken**.
1. In de prompt:
    1. Geef een naam op voor het reken doel
    1. Kies of maak een nieuwe omgeving.
    1. Typ de naam van het script dat u wilt uitvoeren of druk op **Enter** om te bladeren naar het script op de lokale computer.
    1. Beschrijving Kies of u een gegevens referentie wilt maken voor het uitvoeren van uw trainings programma. Als u dit doet, wordt u gevraagd een gegevensset te definiëren in de configuratie van de uitvoering.
        1. Selecteer een van de geregistreerde gegevens sets die u wilt koppelen aan de configuratie uitvoeren een configuratie bestand voor uw gegevensset wordt geopend in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.
    1. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="edit-run-configuration"></a>Uitvoerings configuratie bewerken

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Breid het knoop punt reken cluster uit in het knoop punt **reken clusters** van uw werk ruimte.
1. Klik met de rechter muisknop op de uitvoerings configuratie die u wilt bewerken en selecteer configuratie voor het uitvoeren van de **bewerking**.
1. Een configuratie bestand voor de configuratie van de uitvoering wordt geopend in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="delete-run-configuration"></a>Uitvoerings configuratie verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Vouw het knoop punt berekenings cluster van belang in het knoop punt **reken clusters** uit.
1. Klik met de rechter muisknop op de uitvoerings configuratie die u wilt bewerken en selecteer **configuratie voor uitvoeren verwijderen**.

## <a name="models"></a>Modellen

Zie [modellen](concept-azure-machine-learning-architecture.md#models) voor meer informatie.

### <a name="register-model"></a>Model registreren

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Klik met de rechter muisknop op het knoop punt **modellen** en selecteer **model registreren**.
1. In de prompt:
    1. Geef een naam op voor uw model
    1. Kies of uw model een bestand of map is.
    1. Zoek het model op uw lokale computer.
    1. Een configuratie bestand voor uw model in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

### <a name="view-model-properties"></a>Model eigenschappen weer geven

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **modellen** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het model waarvan u de eigenschappen wilt zien en selecteer **model eigenschappen weer geven**. Een bestand wordt geopend in de editor die uw model eigenschappen bevat.

### <a name="download-model"></a>Model downloaden

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **modellen** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het model dat u wilt downloaden en selecteer **model bestand downloaden**.

### <a name="delete-a-model"></a>Een model verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **modellen** in uw werk ruimte uit.
1. Klik met de rechter muisknop op het model dat u wilt verwijderen en selecteer **model verwijderen**.

## <a name="endpoints"></a>Eindpunten

De VS code-extensie ondersteunt de volgende implementatie doelen:

- Azure Container Instances
- Azure Kubernetes Service

Zie [Web Service-eind punten](concept-azure-machine-learning-architecture.md#web-service-endpoint)voor meer informatie.

### <a name="create-deployments"></a>Implementaties maken

> [!NOTE]
> Het maken van de implementatie werkt momenteel alleen met Conda-omgevingen.

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt van de werk ruimte uit.
1. Klik met de rechter muisknop op het knoop punt **eind punten** en selecteer **service implementeren**.
1. In de prompt:
    1. Kies of u een al geregistreerd model of een lokaal model bestand wilt gebruiken.
    1. Selecteer uw model
    1. Kies het implementatie doel waarnaar u uw model wilt implementeren.
    1. Geef een naam op voor uw model.
    1. Geef het script op dat moet worden uitgevoerd wanneer het model wordt Gescored.
    1. Geef een Conda-afhankelijkheids bestand op.
    1. Een configuratie bestand voor uw implementatie wordt weer gegeven in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.

> [!NOTE]
> U kunt ook met de rechter muisknop op een geregistreerd model in het knoop punt *modellen* klikken en **service implementeren van geregistreerd model** selecteren.

### <a name="delete-deployments"></a>Implementaties verwijderen

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **eind punten** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de implementatie die u wilt verwijderen en selecteer **service verwijderen**.
1. Er wordt een prompt weer gegeven waarin wordt bevestigd dat u de service wilt verwijderen. Selecteer **OK**.

### <a name="manage-deployments"></a>Implementaties beheren

Naast het maken en verwijderen van implementaties, kunt u de instellingen die zijn gekoppeld aan de implementatie weer geven en bewerken.

1. Vouw het knoop punt abonnement uit met uw werk ruimte.
1. Vouw het knoop punt **eind punten** in uw werk ruimte uit.
1. Klik met de rechter muisknop op de implementatie die u wilt beheren:
    - Als u instellingen wilt bewerken, selecteert u **service bewerken**.
        - Een configuratie bestand voor uw implementatie wordt weer gegeven in de editor. Als u tevreden bent met uw configuratie, selecteert u **opslaan en door gaan** of opent u het palet met de VS code opdracht (**> opdracht palet weer geven**) en typt u **Azure ml: opslaan en door gaan**.
    - Selecteer **service-eigenschappen weer geven** om configuratie-instellingen voor implementatie weer te geven.

## <a name="next-steps"></a>Volgende stappen

[Train een afbeeldings classificatie model](tutorial-train-deploy-image-classification-model-vscode.md) met de VS code-extensie.