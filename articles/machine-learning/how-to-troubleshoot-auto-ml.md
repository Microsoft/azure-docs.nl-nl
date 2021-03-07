---
title: Problemen met geautomatiseerde ML experimenten oplossen
titleSuffix: Azure Machine Learning
description: Meer informatie over het oplossen van problemen in uw geautomatiseerde machine learning experimenten.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: f2060d023e0c05fb368b6362bd137c7fd1afe848
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435665"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Problemen met geautomatiseerde ML experimenten in python oplossen

In deze hand leiding vindt u informatie over het identificeren en oplossen van bekende problemen in uw geautomatiseerde machine learning experimenten met de [Azure machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="version-dependencies"></a>Versie afhankelijkheden

**`AutoML` afhankelijkheden voor nieuwere pakket versies zijn compatibiliteit met** verstoringen. Na SDK-versie 1.13.0 zijn modellen niet geladen in oudere Sdk's vanwege incompatibiliteit tussen de oudere versies die in eerdere pakketten zijn vastgemaakt `AutoML` en de nieuwere versies van vandaag.

Verwachte fouten zoals:

* Module niet gevonden fouten zoals,

  `No module named 'sklearn.decomposition._truncated_svd`

* Importeer fouten zoals,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Kenmerk fouten zoals,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

De resoluties zijn afhankelijk van uw `AutoML` SDK-trainings versie:

* Als uw `AutoML` SDK-trainings versie hoger is dan 1.13.0, hebt u `pandas == 0.25.1` en nodig `scikit-learn==0.22.1` .

    * Als er sprake is van een versie die niet overeenkomt, upgradet u scikit-Learn en/of Pandas om de versie te corrigeren met het volgende:

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Als uw `AutoML` SDK-trainings versie lager is dan of gelijk is aan 1.12.0, hebt u `pandas == 0.23.4` en nodig `sckit-learn==0.20.3` .
  * Als er een versie conflict is, downgrade scikit-Learn en/of Pandas om de versie te corrigeren met het volgende:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Instellen

`AutoML` pakket wijzigingen sinds versie 1.0.76 moeten de vorige versie worden verwijderd voordat de nieuwe versie kan worden bijgewerkt.

* **`ImportError: cannot import name AutoMLConfig`**

    Als deze fout optreedt na een upgrade van een SDK-versie vóór v 1.0.76 naar v 1.0.76 of hoger, moet u de fout oplossen door het volgende uit te voeren: `pip uninstall azureml-train automl` en vervolgens `pip install azureml-train-automl` . Het script automl_setup. cmd doet dit automatisch.

* **automl_setup mislukt**

  * Voer automl_setup uit vanaf een Anaconda-prompt in Windows. [Installeer Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Zorg ervoor dat Conda 64-bits versie 4.4.10 of hoger is geïnstalleerd. U kunt de bit controleren met behulp van de `conda info` opdracht. De `platform` moet `win-64` voor Windows of `osx-64` voor Mac zijn. Gebruik de opdracht om de versie te controleren `conda -V` . Als u een vorige versie hebt geïnstalleerd, kunt u deze bijwerken met behulp van de opdracht: `conda update conda` . 32-bits controleren door uit te voeren 

  * Zorg ervoor dat Conda is geïnstalleerd. 

  * Spreek `gcc: error trying to exec 'cc1plus'`

    1. Als de `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` fout is opgetreden, installeert u de gcc-build tools voor uw Linux-distributie. Gebruik bijvoorbeeld op Ubuntu de opdracht `sudo apt-get install build-essential` .

    1. Geef een nieuwe naam als de eerste para meter op automl_setup om een nieuwe Conda-omgeving te maken. Bekijk bestaande Conda-omgevingen met `conda env list` en verwijder ze met `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh mislukt**: als automl_setup_linus. sh mislukt bij Ubuntu Linux met de fout: `unable to execute 'gcc': No such file or directory`

  1. Zorg ervoor dat de uitgaande poorten 53 en 80 zijn ingeschakeld. Op een virtuele machine van Azure kunt u dit doen vanuit de Azure Portal door de virtuele machine te selecteren en op **netwerken** te klikken.
  1. Voer de volgende opdracht uit: `sudo apt-get update`
  1. Voer de volgende opdracht uit: `sudo apt-get install build-essential --fix-missing`
  1. `automl_setup_linux.sh`Opnieuw uitvoeren

* **Configuration. ipynb mislukt**:

  * Voor lokale Conda moet u er eerst voor zorgen dat de `automl_setup` uitvoering is geslaagd.
  * Zorg ervoor dat de subscription_id juist is. Zoek de subscription_id in het Azure Portal door alle services te selecteren en vervolgens op abonnementen. De tekens ' < ' en ' > ' mogen niet worden opgenomen in de subscription_id waarde. `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"`Heeft bijvoorbeeld een geldige indeling.
  * Zorg ervoor dat Inzender of eigenaar toegang heeft tot het abonnement.
  * Controleer of de regio een van de ondersteunde regio's is: `eastus2` , `eastus` , `westcentralus` , `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Zorg ervoor dat u toegang tot de regio hebt met behulp van de Azure Portal.
  
* **Workspace.from_config mislukt**:

  Als de aanroep `ws = Workspace.from_config()` mislukt:

  1. Controleer of de configuratie. ipynb-notebook is uitgevoerd.
  1. Als het notitie blok wordt uitgevoerd vanuit een map die zich niet in de map bevindt waar de `configuration.ipynb` was uitgevoerd, kopieert u de map aml_config en het bestand config.jsop dat item zich in de nieuwe map. Workspace.from_config leest de config.jsop voor de notitieblokmap of de bovenliggende map.
  1. Als er een nieuw abonnement, een resource groep, werk ruimte of regio wordt gebruikt, moet u ervoor zorgen dat u het `configuration.ipynb` notitie blok opnieuw uitvoert. Het is niet mogelijk om config.jsrechtstreeks te wijzigen als de werk ruimte al bestaat in de opgegeven resource groep onder het opgegeven abonnement.
  1. Als u de regio wilt wijzigen, wijzigt u de werk ruimte, de resource groep of het abonnement. `Workspace.create` Er wordt geen werk ruimte gemaakt of bijgewerkt als deze al bestaat, zelfs als de opgegeven regio verschillend is.

## <a name="tensorflow"></a>TensorFlow

Vanaf versie 1.5.0 van de SDK installeert automatische machine learning standaard geen tensor flow-modellen. Als u tensor flow wilt installeren en wilt gebruiken met uw geautomatiseerde ML experimenten, installeert u `tensorflow==1.12.0` via `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy-fouten

* **`import numpy` mislukt in Windows**: sommige Windows-omgevingen zien een fout bij het laden van numpy met de meest recente python-versie 3.6.8. Als u dit probleem ziet, probeert u met python-versie 3.6.7.

* **`import numpy` mislukt**: Controleer de tensor flow-versie in de Automated ml Conda-omgeving. Ondersteunde versies zijn < 1,13. Verwijder tensor flow uit de omgeving als de versie >= 1,13 is.

U kunt de versie van tensor flow controleren en de installatie als volgt verwijderen:

  1. Start een opdracht shell, activeer de Conda-omgeving waar automatisch ml-pakketten worden geïnstalleerd.
  1. Voer `pip freeze` in en zoek naar `tensorflow` , indien gevonden, de weer gegeven versie moet < 1,13
  1. Als de vermelde versie geen ondersteunde versie is, `pip uninstall tensorflow` typt u in de opdracht shell en voert u y in voor bevestiging.

## `jwt.exceptions.DecodeError`

Exact fout bericht: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Voor SDK-versies <= 1.17.0 is de installatie mogelijk een niet-ondersteunde versie van PyJWT. Controleer of de PyJWT-versie in de Automated ml Conda-omgeving een ondersteunde versie is. Dat is PyJWT-versie < 2.0.0.

U kunt de versie van PyJWT als volgt controleren:

1. Start een opdracht shell en activeer de Conda-omgeving waar automatisch ML-pakketten worden geïnstalleerd.

1. Voer `pip freeze` in en zoek naar `PyJWT` , indien gevonden, de weer gegeven versie < 2.0.0

Als de vermelde versie geen ondersteunde versie is:

1. U kunt een upgrade uitvoeren naar de nieuwste versie van de AutoML SDK: `pip install -U azureml-sdk[automl]`

1. Als dat niet haalbaar is, verwijdert u PyJWT uit de omgeving en installeert u de juiste versie als volgt:

    1. `pip uninstall PyJWT` in de opdracht shell en voert u in `y` voor bevestiging.
    1. Installeren met `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Bekijk [hoe u een geautomatiseerd ml experiment configureert met Databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>De prognose R2-Score is altijd nul

 Dit probleem doet zich voor als de verstrekte trainings gegevens een tijd reeks hebben die dezelfde waarde voor de laatste `n_cv_splits`  +  `forecasting_horizon` gegevens punten bevat.

Als dit patroon in uw tijd reeks wordt verwacht, kunt u de primaire metriek overschakelen naar een **genormaliseerd wortel fout**.

## <a name="failed-deployment"></a>Mislukte implementatie

 Voor versies <= 1.18.0 van de SDK kan de basis installatie kopie die is gemaakt voor de implementatie mislukken met de volgende fout: `ImportError: cannot import name cached_property from werkzeug` .

  De volgende stappen kunnen het probleem omzeilen:

  1. Het model pakket downloaden
  1. Pak het pakket uit
  1. Implementeren met behulp van de uitgepakte assets

## <a name="sample-notebook-failures"></a>Voorbeeld notitieblok fouten

 Als een voor beeld van een notebook mislukt met een fout die eigenschap, methode of bibliotheek niet bestaat:

* Zorg ervoor dat de juiste kernel is geselecteerd in de Jupyter Notebook. De kernel wordt weer gegeven in de rechter bovenhoek van de notitie blok pagina. De standaard waarde is *azure_automl*. De kernel wordt opgeslagen als onderdeel van het notitie blok. Als u overschakelt naar een nieuwe Conda-omgeving, moet u de nieuwe kernel in het notitie Blok selecteren.

  * Voor Azure Notebooks moet het python 3,6 zijn.
  * Voor lokale Conda-omgevingen moet dit de Conda-omgevings naam zijn die u hebt opgegeven in automl_setup.

* Om ervoor te zorgen dat het notitie blok voor de SDK-versie is die u gebruikt,
  * Controleer de SDK-versie door `azureml.core.VERSION` in een Jupyter notebook-cel uit te voeren.
  * U kunt de vorige versie van de voorbeeld notitieblokken downloaden van GitHub door de volgende stappen uit te voeren:
    1. Selecteer de `Branch` knop
    1. Ga naar het `Tags` tabblad
    1. De versie selecteren

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [het trainen van een regressie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u het gebruik van geautomatiseerde machine learning op een externe bron kunt trainen](how-to-auto-train-remote.md).

+ Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).