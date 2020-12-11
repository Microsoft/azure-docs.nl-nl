---
title: Azure Notebooks preview configureren en beheren
description: Meer informatie over het beheren van meta gegevens van project, project bestanden, de omgeving van het project en de installatie stappen via zowel de Azure Notebooks gebruikers interface als rechtstreekse terminal toegang.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: fb106132cf0dfb14dc41c1d4b480fd3a1d7fd7fe
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093533"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a>Projecten beheren en configureren in Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Een project in Azure Notebooks preview is in feite een configuratie van de onderliggende virtuele Linux-machine waarin Jupyter-notebooks worden uitgevoerd, samen met een bestandsmap en beschrijvende meta gegevens. 

Met het project dashboard in Azure Notebooks kunt u bestanden beheren en de eigenschappen van het project anders configureren:

- De compute-laag waarop het project wordt uitgevoerd. Dit kan de gratis laag of een virtuele machine van Azure zijn.
- Meta gegevens van het project, met een naam, beschrijving, een id die wordt gebruikt bij het delen van het project en of het project openbaar of privé is.
- De notebook-, gegevens-en andere bestanden van het project, die u als elk ander bestands systeem beheert.
- De omgeving van een project, die u via opstart scripts of rechtstreeks via de Terminal beheert.
- Logboeken, die u via de Terminal kunt openen.

> [!Note]
> De functies voor beheer en configuratie die hier worden beschreven, zijn alleen beschikbaar voor de project eigenaar die in eerste instantie het project heeft gemaakt. U kunt het project echter klonen in uw eigen account, in welk geval u de eigenaar wordt en het project naar wens kunt configureren.

Azure Notebooks start de onderliggende virtuele machine wanneer u een notitie blok of een ander bestand uitvoert. Op de server worden bestanden automatisch opgeslagen en afgesloten na 60 minuten van inactiviteit. U kunt de server ook op elk gewenst moment stoppen met de opdracht **shutdown** (sneltoets: h).

## <a name="compute-tier"></a>Compute-laag

Standaard worden projecten uitgevoerd op de **gratis Compute** -laag. Dit is beperkt tot 4 GB geheugen en 1 GB aan gegevens om misbruik te voor komen. U kunt deze beperkingen overs Laan en de reken kracht verhogen met behulp van een andere virtuele machine die u in een Azure-abonnement hebt ingericht. Zie [How to use data Science virtual machines](use-data-science-virtual-machine.md)voor meer informatie.

## <a name="edit-project-metadata"></a>Meta gegevens van project bewerken

Selecteer in het project dashboard **project instellingen** en selecteer vervolgens het tabblad **informatie** , dat de meta gegevens van het project bevat, zoals beschreven in de volgende tabel. U kunt de meta gegevens van het project op elk gewenst moment wijzigen.

| Instelling | Beschrijving |
| --- | --- |
| Projectnaam | Een beschrijvende naam voor het project die Azure Notebooks gebruikt voor weergave doeleinden. Bijvoorbeeld ' Hallo wereld in python '. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen. Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](create-clone-jupyter-notebooks.md#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, is het een gemeen schappelijke Conventie om een kleine versie van uw project naam te gebruiken waarbij spaties worden omgezet in afbreek streepjes, zoals ' My-notebook-project ' (afgekapt indien nodig om de lengte te bepalen). |
| Openbaar project | Indien ingesteld, staat iedereen met de koppeling toe om het project te openen. Schakel deze optie uit als u een persoonlijk project wilt maken. |
| Klonen verbergen | Als deze instelling is ingesteld, kunnen andere gebruikers geen lijst met klonen zien die voor dit project zijn gemaakt. Het verbergen van klonen is handig voor projecten die worden gedeeld met veel mensen die geen deel uitmaken van dezelfde organisatie, zoals wanneer u een notebook gebruikt voor het door geven van een klasse. |

> [!Important]
>
> Als u de project-ID wijzigt, worden koppelingen naar het project dat u mogelijk eerder hebt gedeeld, ongeldig gemaakt.

## <a name="manage-project-files"></a>Project bestanden beheren

In het project dashboard wordt de inhoud van het map-systeem van het project weer gegeven. U kunt verschillende opdrachten gebruiken om deze bestanden te beheren.

### <a name="create-new-files-and-folders"></a>Nieuwe bestanden en mappen maken

De **+ nieuwe** opdracht (sneltoets: n) maakt nieuwe bestanden of mappen. Wanneer u de opdracht gebruikt, selecteert u eerst het type item dat u wilt maken:

| Item type | Beschrijving | Opdracht gedrag |
| --- | --- | --- |
| **Notebook** | Een Jupyter-notebook | Hiermee wordt een pop-upvenster weer gegeven waarin u de bestands naam en-taal van het notitie blok opgeeft. |
| **Map** | Een submap | Hiermee maakt u een bewerkings veld in de bestands lijst van het project waarin u de mapnaam opgeeft. |
| **Leeg bestand** | Een bestand waarin u alle inhoud kunt opslaan, zoals tekst, gegevens, enzovoort. | Hiermee maakt u een bewerkings veld in de bestands lijst van het project waarin u de bestands naam invoert. |
| **Markdown** | Een bestand met een afkorting. | Hiermee maakt u een bewerkings veld in de bestands lijst van het project waarin u de bestands naam invoert. |

### <a name="upload-files"></a>Bestanden uploaden

De **Upload** opdracht biedt twee opties voor het importeren van gegevens van andere locaties: **van URL** en **van computer**. Zie [werken met gegevens bestanden in azure notebook-projecten](work-with-project-data-files.md)voor meer informatie.

### <a name="select-file-specific-commands"></a>Selecteer Bestands-specifieke opdrachten

Elk item in de bestands lijst van het project bevat opdrachten via een context menu met rechter muisknop:

![Opdrachten in het context menu van een bestand](media/project-file-commands.png)

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Uitvoeren | r (of klik) | Voert een notitieblok bestand uit. Andere bestands typen worden geopend voor weer gave.  |
| Koppeling kopiëren | y | Kopieert een koppeling naar het bestand naar het klem bord. |
| Uitvoeren in Jupyter Lab | v | Voert een notitie blok uit in Jjupyterlab. Dit is een meer ontwikkel gerichte interface dan Jupyter normaal gesp roken. |
| Preview | p | Hiermee opent u een HTML-voor beeld van het bestand. voor notebooks is de preview een alleen-lezen rendering van het notitie blok. Zie de sectie [Preview](#preview) voor meer informatie. |
| Bestand bewerken | i | Hiermee opent u het bestand om het te bewerken. |
| Downloaden | d | Hiermee downloadt u een zip-bestand dat het bestand of de inhoud van een map bevat. |
| Naam wijzigen | een | Vraagt om een nieuwe naam voor het bestand of de map. |
| Verwijderen | x | Vraagt om bevestiging en verwijdert het bestand definitief uit het project. Verwijderingen kunnen niet ongedaan worden gemaakt. |
| Verplaatsen | m | Hiermee verplaatst u een bestand naar een andere map in hetzelfde project. |

#### <a name="preview"></a>Preview

Een voor beeld van een bestand of notitie blok is een alleen-lezen weer gave van de inhoud. het uitvoeren van notebook-cellen is uitgeschakeld. Er wordt een voor beeld weer gegeven voor iedereen die een koppeling naar het bestand of de notebook heeft, maar niet is aangemeld bij Azure Notebooks. Als een gebruiker zich heeft aangemeld, kan hij de notebook klonen naar hun eigen account of het notitie blok downloaden naar hun lokale computer.

De voorbeeld pagina ondersteunt meerdere werkbalk opdrachten met sneltoetsen:

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Delen | s | Hier wordt de pop-up weer gegeven van waaruit u een koppeling kunt verkrijgen, delen met sociale media, HTML ophalen voor insluiten en een e-mail verzendt. |
| Klonen | c  | Kopieer het notitie blok naar uw account. |
| Uitvoeren | r | Voert het notitie blok uit als u dit nog niet hebt toegestaan. |
| Downloaden | d | Hiermee downloadt u een kopie van het notitie blok. |

## <a name="configure-the-project-environment"></a>De project omgeving configureren

Er zijn drie manieren om de omgeving te configureren van de onderliggende virtuele machine waarin uw notitie blokken worden uitgevoerd:

- Een script voor eenmalige initialisatie toevoegen
- Gebruik de omgevings instellingen van het project om installatie stappen op te geven
- Toegang tot de virtuele machine via een Terminal.

Alle soorten project configuratie worden toegepast wanneer de virtuele machine wordt gestart en heeft dus invloed op alle notitie blokken in het project.

### <a name="one-time-initialization-script"></a>Script voor eenmalige initialisatie

De eerste keer dat Azure Notebooks een server voor het project maakt, wordt gezocht naar een bestand in het project met de naam *aznbsetup.sh*. Als dit bestand aanwezig is, wordt het door Azure Notebooks uitgevoerd. De uitvoer van het script wordt opgeslagen in de projectmap als *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Installatie stappen voor de omgeving

U kunt de omgevings instellingen van het project gebruiken om afzonderlijke stappen te maken voor het configureren van de omgeving.

Selecteer in het project dashboard **project instellingen** en selecteer vervolgens het tabblad **omgeving** waarin u de installatie stappen voor het project toevoegt, verwijdert en wijzigt:

![Menu Project instellingen met het tabblad omgeving geselecteerd](media/project-settings-environment-steps.png)

Als u een stap wilt toevoegen, selecteert u eerst **+ toevoegen** en selecteert u vervolgens een stap type in de vervolg keuzelijst **bewerking** :

![Bewerkings kiezer voor een nieuwe omgevings installatie stap](media/project-settings-environment-details.png)

De gegevens die u vervolgens projecteert, zijn afhankelijk van het type bewerking dat u hebt gekozen:

- **Requirements.txt**: Selecteer in de tweede vervolg keuzelijst een *requirements.txt* bestand dat al in het project voor komt. Selecteer vervolgens een python-versie in de derde vervolg keuzelijst die wordt weer gegeven. Als u een *requirements.txt* bestand gebruikt, wordt Azure notebooks uitgevoerd `pip install -r` met het *requirements.txt* -bestand wanneer een notebook server wordt gestart. U hoeft geen expliciet pakketten te installeren vanuit het notitie blok zelf.

- **Shell script**: Selecteer in de tweede vervolg keuzelijst een bash-shell script in het project (meestal een bestand met de extensie *. sh* ) dat opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren.

- **Environment. yml**: Selecteer in de tweede vervolg keuzelijst een *omgeving. yml* -bestand voor python-projecten met een Conda-omgeving.

   > [!WARNING]
   > Omdat dit een service in preview is die nog wordt ontwikkeld, is er momenteel een bekend probleem waarbij de instelling `Environment.yml` niet zoals verwacht op uw project wordt toegepast. In het project en de Jupyter-notebooks in dat project wordt het opgegeven omgevingsbestand momenteel niet geladen.

Wanneer u klaar bent met het toevoegen van stappen, selecteert u **Opslaan**.

### <a name="use-the-terminal"></a>De Terminal gebruiken

In het project dashboard wordt met de opdracht **Terminal** een Linux-terminal geopend waarmee u rechtstreeks toegang krijgt tot de server. Binnen de Terminal kunt u gegevens downloaden, bestanden bewerken of beheren, processen controleren en zelfs gebruikmaken van hulpprogram ma's zoals VI en nano.

> [!Note]
> Als u opstart scripts in de omgeving van uw project hebt, kan het openen van de terminal een bericht weer geven met de melding dat de installatie nog wordt uitgevoerd.

U kunt in de terminal een standaard Linux-opdracht geven. U kunt ook `ls` in de basismap gebruiken om de verschillende omgevingen te zien die zich op de virtuele machine bevinden, zoals *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* en *R*, samen met een *projectmap* die het project bevat:

![Project terminal in Azure Notebooks](media/project-terminal.png)

Als u een specifieke omgeving wilt beïnvloeden, moet u eerst mappen in die omgevings map wijzigen.

Voor de python-omgevingen kunt u de `pip` `conda` map *bin* van elke omgeving zoeken. U kunt ook ingebouwde aliassen gebruiken voor de omgevingen:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Wijzigingen op de server zijn alleen van toepassing op de huidige sessie, behalve voor bestanden en mappen die u in de *projectmap* zelf maakt. Het bewerken van een bestand in de projectmap wordt bijvoorbeeld persistent gemaakt tussen sessies, maar pakketten met `pip install` niet.

> [!Note]
> Als u `python` of gebruikt `python3` , roept u de door het systeem geïnstalleerde versies van python aan, die niet worden gebruikt voor notebooks. U hebt geen machtigingen voor bewerkingen als `pip install` een van beide. Zorg er dus voor dat u de versie-specifieke aliassen gebruikt.

## <a name="access-notebook-logs"></a>Notitie blok-Logboeken openen

Als u problemen ondervindt bij het uitvoeren van een notebook, wordt de uitvoer van Jupyter opgeslagen in een map met de naam *. nb. log*. U kunt deze logboeken openen via de opdracht **Terminal** of het project dashboard.

Normaal gesp roken wanneer u Jupyter lokaal uitvoert, hebt u het mogelijk gestart vanuit een Terminal venster. In het Terminal venster wordt de uitvoer weer gegeven, zoals de kernel-status.

Als u logboeken wilt weer geven, gebruikt u de volgende opdracht in de terminal:

```bash
cat .nb.log
```

U kunt ook de opdracht van een code-cel in een python-notebook gebruiken:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Volgende stappen

- [Procedure: werken met Project-gegevens bestanden](work-with-project-data-files.md)
- [Toegang tot Cloud gegevens in een notitie blok](access-data-resources-jupyter-notebooks.md)
