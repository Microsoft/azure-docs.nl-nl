---
title: Problemen met omgevings afbeeldingen oplossen
titleSuffix: Azure Machine Learning
description: Meer informatie over het oplossen van problemen met omgevings installatie kopieën en pakket installaties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733333"
---
# <a name="troubleshoot-environment-image-builds"></a>Problemen met omgevings afbeeldingen oplossen
Meer informatie over het oplossen van problemen met installatie kopieën van docker-omgeving en pakket installaties.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* De [Azure machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* De [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Om lokaal fouten op te sporen, moet u een werkende docker-installatie op uw lokale systeem hebben.

## <a name="docker-image-build-failures"></a>Build-fouten van docker-installatie kopie
 
Voor het meren deel van fouten bij het opbouwen van de installatie kopie kunt u de hoofd oorzaak vinden in het build-logboek van de installatie kopie.
U kunt het build-bestand van de installatie kopie vinden vanuit de Azure Machine Learning Portal (20 \_ Image \_ Build \_log.txt) of vanuit uw ACR-taken logboeken uitvoeren
 
In de meeste gevallen is het gemakkelijker om fouten lokaal te reproduceren. Controleer het soort fout en voer een van de volgende handelingen uit `setuptools` :

- Conda-afhankelijkheid lokaal installeren `conda install suspicious-dependency==X.Y.Z`
- De PIP-afhankelijkheid lokaal installeren `pip install suspicious-dependency==X.Y.Z`
- Probeer de hele omgeving te realiseren `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Zorg ervoor dat het platform en de interpreter op uw lokale computer overeenkomen met die op het externe systeem. 

### <a name="timeout"></a>Time-out 
 
Er kunnen problemen met de time-out optreden voor verschillende netwerk problemen:
- Lage Internet bandbreedte
- Server problemen
- Grote afhankelijkheid die niet kan worden gedownload met de opgegeven time-outinstellingen voor Conda of PIP
 
Berichten die vergelijkbaar zijn met de onderstaande, duiden op het probleem:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Mogelijke oplossingen:
 
- Probeer een andere bron voor de afhankelijkheid als deze beschikbaar is, zoals Spie gels, Blob Storage of andere python-feeds.
- Conda of PIP bijwerken. Als er een aangepast docker-bestand wordt gebruikt, werkt u de time-outinstellingen bij.
- Sommige PIP-versies hebben bekende problemen. Overweeg een specifieke versie van PIP toe te voegen aan omgevings afhankelijkheden.

### <a name="package-not-found"></a>Pakket niet gevonden

Dit is het meest voorkomende geval bij mislukte builds van installatie kopieën.

- Het Conda-pakket is niet gevonden
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Kan het opgegeven PIP-pakket of de versie niet vinden
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Onjuiste geneste PIP-afhankelijkheid
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Controleer of het pakket bestaat op de opgegeven bronnen. Gebruik [PIP Search](https://pip.pypa.io/en/stable/reference/pip_search/) om PIP-afhankelijkheden te controleren.

`pip search azureml-core`

Gebruik [Conda Search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)voor Conda-afhankelijkheden.

`conda search conda-forge::numpy`

Voor meer opties:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Installatie notities

Zorg ervoor dat de vereiste distributie bestaat voor het opgegeven platform en de versie van de Python-interpreter.

Voor PIP-afhankelijkheden gaat u naar `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` om te zien of de vereiste versie beschikbaar is. Bijvoorbeeld: https://pypi.org/project/azureml-core/1.11.0/#files

Controleer voor Conda-afhankelijkheden pakket op de kanaal opslagplaats.
Voor kanalen die worden onderhouden door Anaconda, Inc. kunt u [hier](https://repo.anaconda.com/pkgs/)controleren.

### <a name="pip-package-update"></a>PIP-pakket update

Tijdens het installeren of bijwerken van een PIP-pakket moet de resolver mogelijk een al geïnstalleerd pakket bijwerken om te voldoen aan de nieuwe vereisten.
Het verwijderen kan mislukken om verschillende redenen met betrekking tot de PIP-versie of de manier waarop de afhankelijkheid is geïnstalleerd.
Het meest voorkomende scenario is dat een afhankelijkheid die door Conda is geïnstalleerd, niet door PIP kan worden verwijderd.
Voor dit scenario kunt u overwegen de afhankelijkheid te verwijderen met `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Installatie problemen

Bepaalde versies van het installatie programma hebben problemen in de pakket resolvers die kunnen leiden tot een build-fout.

Als er een aangepaste basis installatie kopie of dockerfile wordt gebruikt, is het raadzaam om Conda versie 4.5.4 of hoger te gebruiken.

PIP-pakket is vereist voor de installatie van PIP-afhankelijkheden en als er geen versie is opgegeven in de omgeving, wordt de meest recente versie gebruikt.
We raden u aan een bekende versie van PIP te gebruiken om tijdelijke problemen te voor komen of om wijzigingen te verbreken die kunnen worden veroorzaakt door de nieuwste versie van het hulp programma.

U kunt de PIP-versie vastmaken in uw omgeving als u een van de volgende berichten ziet:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Bovendien kan de PIP-installatie worden vastgelopen in een oneindige lus als er onherleidbare-conflicten in de afhankelijkheden zijn. Als u lokaal werkt, downgradeert u de PIP-versie tot < 20,3. In een Conda-omgeving die is gemaakt op basis van een YAML-bestand, is dit probleem alleen zichtbaar als Conda-vervalsing het kanaal met de hoogste prioriteit is. Om het probleem te verhelpen, geeft u in het Conda-specificatie bestand expliciet PIP < 20,3 (! = 20,3 of = 20.2.4-pincode aan andere versie) op als een Conda-afhankelijkheid.

## <a name="service-side-failures"></a>Storingen aan service zijde

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Kan de installatie kopie van MCR/Address niet ophalen voor Container Registry.
Mogelijke problemen:
- De padnaam naar het container register is mogelijk niet juist opgelost. Controleer of de naam van een installatie kopie dubbele slashes en de richting van slashes op Linux vs Windows-hosts juist is.
- Als de ACR achter een Vnet een persoonlijk eind punt in [een niet-ondersteunde regio](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)gebruikt, configureert u de ACR achter een vnet met behulp van het service-eind punt (open bare toegang) vanuit de portal en probeert u het opnieuw.
- Nadat u de ACR achter een VNet hebt geplaatst, moet u ervoor zorgen dat de [arm-sjabloon](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) wordt uitgevoerd. Hiermee kan de werk ruimte communiceren met het ACR-exemplaar.

### <a name="401-error-from-workspace-acr"></a>401 fout in de werk ruimte ACR
Opslag sleutels opnieuw synchroniseren met [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>De omgeving houdt in dat er wordt gewacht tot andere Conda-bewerkingen zijn voltooid... Optreedt
Wanneer er een installatie kopie wordt gemaakt, wordt Conda vergrendeld door de SDK-client. Als het proces is vastgelopen of onjuist is geannuleerd door de gebruiker-Conda, blijft de status vergrendeld. Om dit op te lossen, verwijdert u hand matig het vergrendelings bestand. 

### <a name="custom-docker-image-not-in-registry"></a>Aangepaste docker-installatie kopie is niet in het REGI ster
Controleer of de [juiste tag](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) wordt gebruikt en dat `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` Hiermee wordt Conda uitgeschakeld en worden de geïnstalleerde pakketten van de gebruiker gebruikt.

### <a name="common-vnet-issues"></a>Veelvoorkomende VNet-problemen

1. Controleer of het opslag account, het reken cluster en de Azure Container Registry zich allemaal in hetzelfde subnet van het virtuele netwerk bevinden.
2. Wanneer ACR zich achter een VNet bevindt, kan het niet rechtstreeks worden gebruikt voor het bouwen van installatie kopieën. Het berekenings cluster moet worden gebruikt voor het bouwen van installatie kopieën.
3. Opslag moet mogelijk achter een VNet worden geplaatst wanneer:
    - Het gebruik van een interferentie of privé-wiel gebruiken
    - 403 (niet-geautoriseerde) service fouten bekijken
    - Kan geen afbeeldings gegevens ophalen uit ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Het maken van een installatie kopie mislukt bij het openen van de beveiligde opslag van het netwerk
- ACR-taken werken niet achter het VNet. Als de gebruiker zich achter het VNet bevindt, moet deze een installatie kopie maken met behulp van het berekenings cluster.
- Opslag moet zich achter VNet bevindt om afhankelijkheden van het systeem te kunnen halen. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Kan geen experimenten uitvoeren als netwerk beveiliging is ingeschakeld voor opslag
Wanneer u standaard-docker-installatie kopieën gebruikt en door gebruikers beheerde afhankelijkheden inschakelt, moet u de MicrosoftContainerRegistry-en AzureFrontDoor. FirstParty- [service Tags](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) gebruiken voor allowlist MCR en de bijbehorende afhankelijkheden.

 Zie [VNET inschakelen](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) voor meer informatie.

### <a name="creating-an-icm"></a>Een ICM maken

Wanneer u een ICM maakt/toewijst aan de meta Store, moet u het CSS-ondersteunings ticket toevoegen zodat we het probleem beter kunnen begrijpen.

## <a name="next-steps"></a>Volgende stappen

- [Een machine learning model trainen om bloemen te categoriseren](how-to-train-scikit-learn.md)
- [Een machine learning model trainen met behulp van een aangepaste docker-installatie kopie](how-to-train-with-custom-image.md)