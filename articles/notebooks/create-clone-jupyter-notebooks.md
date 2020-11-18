---
title: Jupyter-notebooks maken en klonen-Azure Notebooks preview
description: Azure Notebooks preview-projecten een verzameling van notitie blokken en gerelateerde bestanden beheren, die u kunt maken van een nieuwe of een kloon vanuit een andere bron.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: 8e5930178b7951ff5f701228cee4ea7cb5f0e6da
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844383"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Projecten maken en klonen in Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks worden uw Jupyter-notebooks en gerelateerde bestanden ingedeeld in logische groepen die *projecten* worden genoemd. U maakt eerst een project als container en vervolgens maakt of kloont u een of meer notitie blokken in een map naast andere project bestanden. (Dit proces wordt in de [zelf studie](tutorial-create-run-jupyter-notebook.md)gedemonstreerd.)

Een project houdt ook meta gegevens en andere configuratie-instellingen bij die van invloed zijn op de server waarop notitie blokken worden uitgevoerd, met inbegrip van aangepaste installatie stappen en pakket installatie. Zie [projecten beheren en configureren](configure-manage-azure-notebooks-projects.md)voor meer informatie.

## <a name="use-the-my-projects-dashboard"></a>Het dash board mijn projecten gebruiken

In het dash board **Mijn projecten** op kunt `https://notebooks.azure.com/<userID>/projects` u projecten bekijken, beheren en maken:

[![Dash board van mijn projecten in Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Wat u op het dash board kunt doen, is afhankelijk van het feit of u bent aangemeld met het account dat eigenaar is van de gebruikers-ID:

| Opdracht | Beschikbaar voor | Beschrijving |
| --- | --- | --- |
| **Uitvoeren** | Eigenaar | Start de Project Server en opent de projectmap in Jupyter. (In de meeste gevallen gaat u eerst naar een projectmap en vervolgens een notitie blok starten.) |
| **Downloaden** | Iedereen | Hiermee wordt een kopie van het geselecteerde project gedownload als een ZIP-bestand. |
| **Delen** | Iedereen | Hier wordt de pop-up weer gegeven waarmee u een URL kunt verkrijgen voor een geselecteerd project, delen naar sociale media, een e-mail verzendt met de URL en de code voor HTML of prijs verlaging kunt verkrijgen voor met de badge ' Launch Notebook ' (Zie [een Launch badge verkrijgen](#obtain-a-launch-badge)) met de URL. |
| **Verwijderen** | Eigenaar | Hiermee verwijdert u het geselecteerde project. Deze bewerking kan niet ongedaan worden gemaakt. |
| **Terminal** | Eigenaar | Hiermee wordt de Project Server gestart en vervolgens wordt er een nieuw browser venster geopend met de bash-terminal voor die server. |
| **+ Nieuw project** | Eigenaar | Hiermee maakt u een nieuw project. Zie [een nieuw project maken](#create-a-new-project). |
| **GitHub opslag plaats uploaden** | Eigenaar | Hiermee wordt een project uit GitHub geïmporteerd. [Importeer een project uit github](#import-a-project-from-github). |
| **Klonen** | Iedereen | Hiermee wordt een geselecteerd project naar uw eigen account gekopieerd. Hiermee wordt u gevraagd om u aan te melden als dat nog niet het geval is. Zie [een project klonen](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Een Launch badge verkrijgen

Wanneer u de opdracht **delen** gebruikt en het tabblad **insluiten** selecteert, kunt u HTML-code of-prijs opwaarderen die een ' notitie blok starten ' badge maakt:

![Notebook-badge starten](https://notebooks.azure.com/launch.png)

Als u geen Azure Notebooks project hebt, kunt u rechtstreeks een koppeling maken die van GitHub wordt gekloond met behulp van de volgende sjablonen, waarbij u de juiste namen voor de gebruikers naam en de opslag plaats vervangt:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Een nieuw project maken

Wanneer u de **+ New Project** -opdracht gebruikt, wordt er een pop-upvenster **nieuw project maken** Azure notebooks weer gegeven. Voer in dit pop-upvenster de volgende gegevens in en selecteer vervolgens **maken**:

| Veld | Beschrijving |
| --- | --- |
| Projectnaam | Een beschrijvende naam voor het project die Azure Notebooks gebruikt voor weergave doeleinden. Bijvoorbeeld ' mijn notitie blok-project '. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, is het een gemeen schappelijke Conventie om een kleine versie van uw project naam te gebruiken waarbij spaties worden omgezet in afbreek streepjes, zoals ' My-notebook-project ' (afgekapt indien nodig om de lengte te bepalen). |
| Openbaar | Indien ingesteld, staat iedereen met de koppeling toe om het project te openen. Schakel deze optie uit als u een persoonlijk project wilt maken. |
| Dit project initialiseren met een Leesmij-bestand | Als deze instelling is ingesteld, wordt in het project een standaard *README.MD* -bestand gemaakt. In een *README.MD* -bestand kunt u indien gewenst documentatie voor uw project opgeven. |

### <a name="reserved-project-ids"></a>Gereserveerde project-Id's

De volgende gereserveerde woorden kunnen niet worden gebruikt door zichzelf als project-Id's. Deze gereserveerde woorden kunnen echter worden gebruikt als onderdeel van langere project-Id's.

- Info
- account
- beheer
- api
- blog
- leslokaal
- inhoud
- Dashboard
- verkennen
- FAQ
- help
- html
- startpagina
- importeren
- bibliotheek
- beheer
- nieuw
- notebook
- notebooks
- pdf
- preview
- koers
- profiel
- zoeken
- status
- ondersteuning
- test

Als u een van deze woorden als project-ID probeert te gebruiken, worden de pop-up **nieuwe project** -en **project instellingen** maken popups, ' bibliotheek-id is een gereserveerde id '.

Omdat een project-ID ook deel uitmaakt van de URL van een project, kan het gebruik van bepaalde tref woorden, zoals ' advertisements ', worden geblokkeerd door de software van ad blocker. In dergelijke gevallen gebruikt u een ander woord in de project-ID.

## <a name="import-a-project-from-github"></a>Een project uit GitHub importeren

U kunt eenvoudig een volledige open bare GitHub-opslag plaats importeren als een project met inbegrip van alle gegevens-en *README.MD* -bestanden. Gebruik de **Upload github opslag plaats** opdracht, geef de volgende details op in het pop-upvenster en selecteer vervolgens **importeren**:

| Veld | Beschrijving |
| --- | --- |
| GitHub-opslagplaats | De naam van de bron opslagplaats op github.com. Als u bijvoorbeeld de Jupyter-notebooks voor Azure Cognitive Services wilt klonen [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) , voert u ' micro soft/cognitieve-Services-notebooks ' in.  |
| Recursief klonen | GitHub-opslag plaatsen kunnen meerdere onderliggende opslag plaatsen bevatten. Stel deze optie in als u de bovenliggende en alle onderliggende opslag plaatsen wilt klonen. Omdat het mogelijk is dat een opslag plaats veel onderliggende items heeft, laat u deze optie uitgeschakeld, tenzij u er zeker van bent dat u ze nodig hebt. |
| Projectnaam | Een beschrijvende naam voor het project die Azure Notebooks gebruikt voor weergave doeleinden. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, is het een gemeen schappelijke Conventie om een kleine versie van uw project naam te gebruiken waarbij spaties worden omgezet in afbreek streepjes, zoals ' My-notebook-project ' (afgekapt indien nodig om de lengte te bepalen). |
| Openbaar | Indien ingesteld, staat iedereen met de koppeling toe om het project te openen. Schakel deze optie uit als u een persoonlijk project wilt maken. |

Als u een opslag plaats importeert vanuit GitHub, wordt ook de geschiedenis geïmporteerd. U kunt standaard Git-opdrachten van de Terminal gebruiken om nieuwe wijzigingen door te voeren, wijzigingen op te halen van GitHub, enzovoort.

## <a name="clone-a-project"></a>Een project klonen

Bij het klonen wordt een kopie gemaakt van een bestaand project in uw eigen account, waar u vervolgens elk notitie blok of ander bestand in het project kunt uitvoeren en wijzigen. U kunt ook klonen gebruiken om kopieën te maken van uw eigen projecten waarin u experimenten of andere werkzaamheden uitvoert zonder het oorspronkelijke project te verstoren.

Een project klonen:

1. Klik in het dash board **Mijn projecten** met de rechter muisknop op het gewenste project en selecteer **kloon** (sneltoets: c).

    ![De opdracht kopiëren in het context menu van het project](media/clone-command.png)

1. Voer in het menu **kloon project** de naam en id in voor de kloon en geef op of de kloon openbaar is. Deze instellingen zijn hetzelfde als voor een [Nieuw project](#create-a-new-project).

    ![Menu Project klonen](media/clone-project.png)

1. Nadat u de knop **klonen** hebt geselecteerd, gaat Azure notebooks rechtstreeks naar de kopie.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)
- [Procedure: Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: pakketten installeren vanuit een notitie blok](install-packages-jupyter-notebook.md)
- [Procedure: een diavoorstelling presen teren](present-jupyter-notebooks-slideshow.md)
- [Procedure: Werken met gegevensbestanden](work-with-project-data-files.md)
- [Procedure: Toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
- [Procedure: Azure Machine Learning gebruiken](../machine-learning/samples-notebooks.md)