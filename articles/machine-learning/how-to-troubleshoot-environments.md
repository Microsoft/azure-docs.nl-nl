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
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519126"
---
# <a name="troubleshoot-environment-image-builds"></a>Problemen met omgevings afbeeldingen oplossen

Meer informatie over het oplossen van problemen met installatie kopieën van docker-omgeving en pakket installaties.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* De [Azure machine learning SDK](/python/api/overview/azure/ml/install).
* De [Azure CLI](/cli/azure/install-azure-cli).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Om lokaal fouten op te sporen, moet u een werkende docker-installatie op uw lokale systeem hebben.

## <a name="docker-image-build-failures"></a>Build-fouten van docker-installatie kopie
 
Voor de meeste mislukte installatie kopieën vindt u de hoofd oorzaak in het build-logboek van de installatie kopie.
Zoek het build-bestand van de installatie kopie van de Azure Machine Learning Portal (20 \_ Image \_ Build \_log.txt) of vanuit uw Azure container Registry taak uitvoer Logboeken.
 
Doorgaans is het gemakkelijker om fouten lokaal te reproduceren. Controleer het soort fout en voer een van de volgende handelingen uit `setuptools` :

- Een Conda-afhankelijkheid lokaal installeren: `conda install suspicious-dependency==X.Y.Z` .
- Een PIP-afhankelijkheid lokaal installeren: `pip install suspicious-dependency==X.Y.Z` .
- Probeer de hele omgeving te realiseren: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Zorg ervoor dat het platform en de interpreter op uw lokale Compute-Cluster overeenkomen met de platforms op het externe Compute-Cluster. 

### <a name="timeout"></a>Time-out 
 
De volgende netwerk problemen kunnen time-outfouten veroorzaken:

- Lage Internet bandbreedte
- Server problemen
- Grote afhankelijkheden die niet kunnen worden gedownload met de opgegeven time-outinstellingen voor Conda of PIP
 
Berichten die vergelijkbaar zijn met de volgende voor beelden, geven het probleem aan:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Als er een fout bericht wordt weer gegeven, kunt u een van de volgende oplossingen proberen:
 
- Probeer een andere bron, zoals mirrors, Azure Blob Storage of andere python-feeds, voor de afhankelijkheid.
- Conda of PIP bijwerken. Als u een aangepast docker-bestand gebruikt, werkt u de time-outinstellingen bij.
- Sommige PIP-versies hebben bekende problemen. U kunt een specifieke versie van PIP toevoegen aan de omgevings afhankelijkheden.

### <a name="package-not-found"></a>Pakket niet gevonden

De volgende fouten worden het meest gebruikt voor het mislukken van installatie kopieën:

- Kan het Conda-pakket niet vinden:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Het opgegeven PIP-pakket of de versie kan niet worden gevonden:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Ongeldige afhankelijkheid van geneste PIP:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Controleer of het pakket bestaat op de opgegeven bronnen. [PIP-zoek opdracht](https://pip.pypa.io/en/stable/reference/pip_search/) gebruiken om PIP-afhankelijkheden te controleren:

- `pip search azureml-core`

Voor Conda-afhankelijkheden gebruikt u [Conda Search](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Voor meer opties kunt u het volgende proberen:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Installatie notities

Zorg ervoor dat de vereiste distributie bestaat voor het opgegeven platform en de versie van de Python-interpreter.

Voor PIP-afhankelijkheden gaat u naar `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` om te zien of de vereiste versie beschikbaar is. Ga naar https://pypi.org/project/azureml-core/1.11.0/#files om een voor beeld te bekijken.

Controleer voor Conda-afhankelijkheden het pakket in de kanaal opslagplaats.
Ga naar de [pagina Anaconda-pakketten](https://repo.anaconda.com/pkgs/)voor kanalen die worden onderhouden door Anaconda, Inc..

### <a name="pip-package-update"></a>PIP-pakket update

Tijdens een installatie of een update van een PIP-pakket moet de resolver mogelijk een eerder geïnstalleerd pakket bijwerken om te voldoen aan de nieuwe vereisten.
Installatie kan niet worden uitgevoerd om diverse redenen met betrekking tot de PIP-versie of de manier waarop de afhankelijkheid is geïnstalleerd.
Het meest voorkomende scenario is dat een afhankelijkheid die door Conda is geïnstalleerd, niet door PIP kan worden verwijderd.
Voor dit scenario kunt u overwegen de afhankelijkheid te verwijderen met `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Installatie problemen

Bepaalde versies van het installatie programma hebben problemen in de pakket resolvers die kunnen leiden tot een build-fout.

Als u een aangepaste basis installatie kopie of Dockerfile gebruikt, raden wij u aan Conda versie 4.5.4 of hoger te gebruiken.

Er is een PIP-pakket vereist om PIP-afhankelijkheden te installeren. Als er geen versie is opgegeven in de omgeving, wordt de meest recente versie gebruikt.
We raden u aan een bekende versie van PIP te gebruiken om tijdelijke problemen te voor komen of om wijzigingen te verbreken die de nieuwste versie van het hulp programma kan veroorzaken.

U kunt de PIP-versie vastmaken in uw omgeving als u het volgende bericht ziet:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Fout in subproces van PIP:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

PIP-installatie kan in een oneindige lus vastzitten als er onherleidbare-conflicten in de afhankelijkheden zijn. Als u lokaal werkt, downgradet u de PIP-versie naar < 20,3. In een Conda-omgeving die is gemaakt op basis van een YAML-bestand, ziet u dit probleem alleen als Conda-vervalsing het kanaal met de hoogste prioriteit is. Om het probleem te verhelpen, geeft u in het Conda-specificatie bestand expliciet PIP < 20,3 (! = 20,3 of = 20.2.4-pincode aan andere versie) op als een Conda-afhankelijkheid.

## <a name="service-side-failures"></a>Storingen aan de service zijde

Raadpleeg de volgende scenario's om mogelijke storingen aan de service zijde op te lossen.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>U kunt geen installatie kopie uit een container register halen of het adres kan niet worden omgezet voor een container register

Mogelijke problemen:
- De naam van het pad naar het container register is mogelijk niet juist opgelost. Controleer of de naam van de installatie kopie dubbele slashes en de richting van slashes op Linux versus Windows-hosts juist is.
- Als een container register achter een virtueel netwerk een persoonlijk eind punt in [een niet-ondersteunde regio](../private-link/private-link-overview.md#availability)gebruikt, configureert u het container register met behulp van het service-eind punt (open bare toegang) vanuit de portal en probeert u het opnieuw.
- Nadat u het container register achter een virtueel netwerk hebt geplaatst, voert u de [Azure Resource Manager sjabloon](./how-to-network-security-overview.md) uit zodat de werk ruimte kan communiceren met het container register exemplaar.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Er wordt een 401-fout weer geven in een werkruimte container register

Synchroniseer opslag sleutels opnieuw met behulp van [WS.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>De omgeving houdt een wacht tijd voor het volt ooien van andere Conda-bewerkingen... optreedt

Wanneer er een installatie kopie wordt gemaakt, wordt Conda vergrendeld door de SDK-client. Als het proces is vastgelopen of onjuist is geannuleerd door de gebruiker, blijft Conda de vergrendelde status. U kunt dit probleem oplossen door het vergrendelings bestand hand matig te verwijderen. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Uw aangepaste docker-installatie kopie bevindt zich niet in het REGI ster

Controleer of de [juiste tag](./how-to-use-environments.md#create-an-environment) wordt gebruikt en dat `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` Hiermee wordt Conda uitgeschakeld en worden de geïnstalleerde pakketten van de gebruiker gebruikt.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>U krijgt een van de volgende algemene problemen met het virtuele netwerk

- Controleer of het opslag account, het berekenings cluster en het container register zich allemaal in hetzelfde subnet van het virtuele netwerk bevinden.
- Wanneer het container register zich achter een virtueel netwerk bevindt, kan het niet rechtstreeks worden gebruikt voor het bouwen van installatie kopieën. U moet het berekenings cluster gebruiken om installatie kopieën te maken.
- Opslag moet mogelijk achter een virtueel netwerk worden geplaatst als u:
    - Gebruik de afleiding of het persoonlijke wiel.
    - Zie 403 (niet-geautoriseerde) service fouten.
    - Kan geen afbeeldings Details ophalen van Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Het maken van de installatie kopie mislukt wanneer u probeert toegang te krijgen tot met het netwerk beveiligde opslag

- Azure Container Registry taken werken niet achter een virtueel netwerk. Als de gebruiker zijn container register achter een virtueel netwerk heeft, moeten ze het Compute-cluster gebruiken om een installatie kopie te bouwen.
- Opslag moet zich achter een virtueel netwerk bevindt om afhankelijkheden ervan te halen.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>U kunt geen experimenten uitvoeren wanneer de functie netwerk beveiliging is ingeschakeld voor opslag

Als u gebruikmaakt van standaard-docker-installatie kopieën en door gebruikers beheerde afhankelijkheden in te scha kelen, gebruikt u de MicrosoftContainerRegistry-en AzureFrontDoor. FirstParty- [service Tags](./how-to-network-security-overview.md) voor allowlist Azure container Registry en de bijbehorende afhankelijkheden.

 Zie [virtuele netwerken inschakelen](./how-to-network-security-overview.md)voor meer informatie.

### <a name="you-need-to-create-an-icm"></a>U moet een ICM maken

Wanneer u een ICM maakt/toewijst aan de meta Store, neemt u het CSS-ondersteunings ticket op zodat we het probleem beter kunnen begrijpen.

## <a name="next-steps"></a>Volgende stappen

- [Een machine learning model trainen om bloemen te categoriseren](how-to-train-scikit-learn.md)
- [Een machine learning model trainen met behulp van een aangepaste docker-installatie kopie](how-to-train-with-custom-image.md)
