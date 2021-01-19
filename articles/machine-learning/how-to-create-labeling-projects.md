---
title: Een gegevenslabelproject maken
titleSuffix: Azure Machine Learning
description: Leer hoe u labelprojecten maakt en uitvoert om gegevens te taggen voor machine learning. Gebruik met ML ondersteund labelen, of handmatig HTIL-labelen (Human-in-the-loop) om te helpen met de taak.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 854504347409efb4f0eafff0d776db23ca9fda07
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059837"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Een gegevenslabelproject maken en labels exporteren 

Leer hoe u gegevenslabelprojecten maakt en uitvoert om gegevens te taggen in Azure Machine Learning.  Gebruik met ML ondersteund labelen van gegevens, of handmatig HTIL-labelen (Human-in-the-loop) om te helpen met de taak.


## <a name="data-labeling-capabilities"></a>Mogelijkheden voor het labelen van gegevens

> [!Important]
> Er worden momenteel alleen labelprojecten voor afbeeldingsclassificatie en objectidentificatie ondersteund. Daarnaast moeten de gegevensafbeeldingen beschikbaar zijn in een Azure Blob-gegevensopslag. (Als u geen bestaande gegevensopslag hebt, kunt u afbeeldingen uploaden tijdens het maken van het project.)

Met de functie voor gegevens labelen van Azure Machine Learning kunt u gebruikmaken van een centrale locatie voor het maken, beheren en bewaken van labelprojecten:
 - Coördineer gegevens, labels en teamleden om labeltaken op efficiënte wijze te beheren. 
 - Houd de voortgang bij en onderhoud de wachtrij met onvoltooide labeltaken.
 - Start en stop het project en beheer de voortgang van het labelproces.
 - Controleer de gelabelde gegevens en exporteer gegevens in COCO-indeling of als een Azure Machine Learning-gegevensset.

## <a name="prerequisites"></a>Vereisten

* De gegevens die u wilt labelen, hetzij in lokale bestanden of in Azure Blob-opslag.
* De set labels die u wilt toepassen.
* De instructies voor het labelen.
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="create-a-data-labeling-project"></a>Een gegevenslabelproject maken

Labelprojecten worden beheerd vanuit Azure Machine Learning. U gebruikt de pagina **Labelprojecten** om uw projecten te beheren.

Als uw gegevens zich al in Azure Blob-opslag bevinden, moet u deze beschikbaar maken als gegevensopslag voordat u het labelproject maakt. Raadpleeg voor een voorbeeld van het gebruik van gegevensopslag [Zelfstudie: Uw eerst labelproject maken voor afbeeldingsclassificatie](tutorial-labeling.md).

Als u een project wilt maken, selecteert u **Project toevoegen**. Geef het project een geschikte naam en selecteer **Type labeltaak**. Projectnaam kan niet opnieuw worden gebruikt, zelfs niet als het project in de toekomst wordt verwijderd.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Wizard Labelproject maken":::

* Kies **Afbeeldingsclassificatie met meerdere klassen** voor projecten wanneer u slechts *één label* uit een set labels wilt toepassen op een afbeelding.
* Kies **Afbeeldingsclassificatie met meerdere labels** voor projecten wanneer u *een of meer* labels uit een set labels wilt toepassen op een afbeelding. Een foto van een hond kan bijvoorbeeld worden gelabeld met zowel *hond* als *overdag*.
* Kies **Object-id (begrenzingsvak)** voor projecten wanneer u een label en een begrenzingsvak wilt toewijzen aan elk object in een afbeelding.

Selecteer **Volgende** wanneer u klaar bent om door te gaan.

## <a name="specify-the-data-to-label"></a>Geef de gegevens op die u wilt labelen

Als u al een gegevensset hebt gemaakt die uw gegevens bevat, selecteert u deze set in de vervolgkeuzelijst **Een bestaande gegevensset selecteren**. U kunt ook **Een gegevensset maken** selecteren om een bestaande Azure-gegevensopslag te gebruiken of lokale bestanden te uploaden.

> [!NOTE]
> Een project kan niet meer dan 500.000 afbeeldingen bevatten.  Als uw gegevensset meer afbeeldingen bevat, worden alleen de eerste 500.000 afbeeldingen geladen.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Een gegevensset maken uit een Azure-gegevensopslag

In veel gevallen is het een prima idee om gewoon lokale bestanden te uploaden. Maar [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) biedt een snellere en krachtigere manier om grote hoeveelheden gegevens over te brengen. We raden Storage Explorer aan als de standaardmanier om bestanden te verplaatsen.

Een gegevensset maken op basis van gegevens die u al hebt opgeslagen in Azure Blob-opslag:

1. Selecteer **Een gegevensset maken** > **Uit gegevensopslag**.
1. Wijs een **Naam** toe aan de gegevensset.
1. Kies **Bestand** als **Type gegevensset**.  Alleen gegevenssets van het type Bestand worden ondersteund.
1. Selecteer de gegevensopslag.
1. Als uw gegevens zich in een submap bevinden binnen uw Blob-opslag, kiest u **Bladeren** om het pad te selecteren.
    * Voeg /* * toe aan het pad om alle bestanden in de submappen van het geselecteerde pad op te nemen.
    * Voeg * */* .* toe om alle gegevens in de huidige container en de bijbehorende submappen op te nemen.
1. Geef een beschrijving voor de gegevensset.
1. Selecteer **Next**.
1. Bevestig de details. Selecteer **Terug** om de instellingen te wijzigen of **Maken** om de gegevensset te maken.


### <a name="create-a-dataset-from-uploaded-data"></a>Een gegevensset maken uit geüploade gegevens

Uw gegevens rechtstreeks uploaden:

1. Selecteer **Een gegevensset maken** > **Uit lokale bestanden**.
1. Wijs een **Naam** toe aan de gegevensset.
1. Kies Bestand als **Type gegevensset**.
1. *Optioneel:* Selecteer **Geavanceerde instellingen** om de gegevensopslag, de container en het pad naar uw gegevens aan te passen.
1. Selecteer **Bladeren** om de lokale bestanden te selecteren die u wilt uploaden.
1. Geef een beschrijving voor de gegevensset.
1. Selecteer **Next**.
1. Bevestig de details. Selecteer **Terug** om de instellingen te wijzigen of **Maken** om de gegevensset te maken.

De gegevens worden geüpload naar de Blob-standaardopslag (workspaceblobstore) van uw Machine Learning-werkruimte.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Incrementeel vernieuwen configureren

Als u van plan bent nieuwe afbeeldingen toe te voegen aan uw gegevensset, gebruikt u incrementeel vernieuwen om deze nieuwe afbeeldingen toe te voegen aan uw project.   Als **Incrementeel vernieuwen** is ingeschakeld, wordt de gegevensset regelmatig gecontroleerd op nieuwe afbeeldingen die moeten worden toegevoegd aan een project, op basis van de voltooiingssnelheid voor het labelen.   De controle op nieuwe gegevens stopt wanneer het project de maximale 500.000 afbeeldingen bevat.

Als u meer afbeeldingen aan uw project wilt toevoegen, gebruikt u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om te uploaden naar de juiste map in de Blob-opslag. 

Schakel het selectievakje voor **Incrementele vernieuwing inschakelen** in wanneer u wilt dat het project doorlopend controleert op nieuwe gegevens in de gegevensopslag. Deze gegevens worden één keer per dag naar uw project gehaald als dit is ingeschakeld.

Schakel dit selectievakje uit als u niet wilt dat nieuwe afbeeldingen die worden weergegeven in de gegevensopslag, worden toegevoegd aan het project.

U vindt de tijdstempel voor de laatste vernieuwing in de sectie **Incrementeel vernieuwen** van het tabblad **Details** voor het project.


## <a name="specify-label-classes"></a>Labelklassen opgeven

Geef op de pagina **Labelklassen** de set klassen op om uw gegevens te categoriseren. De nauwkeurigheid en snelheid van labelaars hangt samen met de mogelijkheid om te kiezen uit de klassen. Gebruik bijvoorbeeld, in plaats van het hele geslacht en soort van planten, alleen een veldcode op of gebruik een afkorting voor het plantengeslacht.

Voer één label per rij in. Gebruik de knop **+** om een nieuwe rij toe te voegen. Als u meer dan 3 of 4 labels hebt, maar minder dan 10, is het een goed idee om de namen vooraf te laten gaan door een cijfer (‘1:’, ‘2:’) zodat labelaars de cijfertoetsen kunnen gebruiken om sneller te werken.

## <a name="describe-the-data-labeling-task"></a>De labeltaak beschrijven

Het is belangrijk om de labeltaak duidelijk uit te leggen. Op de pagina **Instructies voor labelen** kunt u een koppeling naar een externe site toevoegen voor labelinstructies, of u kunt instructies bieden in het bewerkingsvak op de pagina. Houd de instructies taakgericht en geschikt voor de doelgroep. Houd rekening met deze vragen:

* Welke labels krijgen labelaars te zien, en hoe gaan ze een keuze maken? Is er een referentietekst waarnaar kan worden verwezen?
* Wat moeten ze doen als er geen geschikt label aanwezig is?
* Wat moeten ze doen als meerdere labels geschikt zijn?
* Welke drempelwaarde voor betrouwbaarheid moeten ze toepassen op een label? Wilt u dat ze een gok wagen, als ze het niet zeker weten?
* Wat moeten labelaars doen met belangrijke objecten die gedeeltelijk buiten beeld vallen of overlappen?
* Wat moeten ze doen wanneer een belangrijk object wordt afgesneden door de rand van de afbeelding?
* Wat moeten ze doen als ze een verkeerd label hebben toegepast?

Voor begrenzingsvakken zijn dit een aantal belangrijke vragen:

* Hoe wordt het begrenzingsvak voor deze taak gedefinieerd? Moet dit vak zich helemaal binnen in het object bevinden, of juist aan de buitenkant? Moet het vak zo klein mogelijk om het object vallen, of mag er wat speelruimte zijn?
* Hoe zorgvuldig en consistent verwacht u dat labelaars zijn bij het definiëren van begrenzingsvakken?
* Hoe moet een object worden gelabeld dat slechts gedeeltelijk worden weergegeven in de afbeelding? 
* Hoe moet een object worden gelabeld dat gedeeltelijk wordt bedekt door een ander object?

>[!NOTE]
> Zorg ervoor dat labelaars de eerste 9 labels kunnen selecteren met de cijfertoetsen 1 tot en met 9.

## <a name="use-ml-assisted-data-labeling"></a>Met ML ondersteund labelen van gegevens gebruiken

Op de pagina **Met ML ondersteund labelen** kunt u automatische machine learning-modellen activeren om de labeltaak te versnellen. Aan het begin van het labelproject worden de afbeeldingen in een willekeurige volgorde gerangschikt om mogelijke vooroordelen te verminderen. Eventuele vooroordelen die al aanwezig zijn in de gegevensset, worden echter wel weerspiegeld in het getrainde model. Als bijvoorbeeld 80% van uw afbeeldingen van één klasse zijn, dan is ongeveer 80% van de gegevens die zijn gebruikt om het model te trainen, ook van deze klasse. Deze training omvat geen actief onderwijs.

Selecteer *Met ML ondersteund labelen inschakelen* en geef een GPU op om ondersteund labelen in te schakelen, wat bestaat uit twee fasen:
* Clustering
* Prelabelen

De exacte hoeveelheid gelabelde afbeeldingen die nodig is om ondersteund labelen te starten, is geen vast aantal.  De hoeveelheid kan aanzienlijk verschillen tussen het ene labelproject en het andere. Voor sommige projecten is het soms mogelijk om prelabel- of clustertaken te zien nadat 300 afbeeldingen handmatig zijn gelabeld. Voor Met ML ondersteund labelen wordt gebruikgemaakt van een techniek genaamd *Kennisoverdracht*, waarbij een vooraf getraind model wordt gebruikt om het trainingsproces sneller te kunnen starten. Als de klassen van uw gegevensset vergelijkbaar zijn met die in het vooraf getrainde model, zijn er mogelijk al na slechts een paar honderd handmatig gelabelde afbeeldingen pre-labels beschikbaar. Als uw gegevensset aanzienlijk afwijkt van de gegevens die worden gebruikt om het model vooraf te trainen, kan het ook veel langer duren.

Aangezien de definitieve labels nog steeds afhankelijk zijn van invoer van de labelaar, wordt deze technologie soms *HITL*-labelen (Human-in-the-loop) genoemd.

> [!NOTE]
> Het labelen van gegevens met behulp van ML biedt geen ondersteuning voor standaardopslagaccounts die zijn beveiligd achter een [virtueel netwerk](how-to-network-security-overview.md). U moet een niet-standaard opslagaccount gebruiken voor het labelen van gegevens met behulp van ML. Het niet-standaard opslagaccount kan worden beveiligd achter het virtuele netwerk. 

### <a name="clustering"></a>Clustering

Nadat een bepaald aantal labels is verzonden, begint het machine learning-model voor de afbeeldingsclassificatie met het groeperen van gelijksoortige afbeeldingen.  Deze gelijksoortige afbeeldingen worden op hetzelfde scherm getoond aan de labelaars, om handmatig taggen te versnellen. Clustering is met name handig wanneer de labelaar een raster met 4, 6 of 9 afbeeldingen bekijkt. 

Zodra een machine learning-model is getraind met uw handmatig gelabelde gegevens, wordt het model afgekapt tot de laatste volledig verbonden laag. Niet-gelabelde afbeeldingen worden vervolgens doorgegeven via het afgekapte model met behulp van een proces dat meestal insluiting (of ‘featurization’) wordt genoemd. Hierbij wordt elke afbeelding ingesloten in een hoog-dimensionale ruimte die is gedefinieerd met deze modellaag. Afbeeldingen die in de ruimte het dichtst bij elkaar liggen, worden gebruikt voor clustertaken. 

De clusteringsfase wordt niet weergegeven voor objectdetectiemodellen.

### <a name="prelabeling"></a>Prelabelen

Wanneer voldoende afbeeldingslabels zijn verzonden, wordt een classificatiemodel gebruikt om afbeeldingstags te voorspellen. Of er wordt een objectdetectiemodel gebruikt om begrenzingsvakken te voorspellen. De labelaar ziet nu pagina's met voorspelde labels die al aanwezig zijn voor elke afbeelding. Voor objectdetectie worden ook voorspelde vakken weergegeven. Vervolgens is het zaak om deze voorspellingen te beoordelen en eventuele verkeerd gelabelde afbeeldingen te corrigeren voordat de pagina wordt verzonden.  

Zodra een machine learning-model is getraind met uw handmatig gelabelde gegevens, wordt het model geëvalueerd in een testset met handmatig gelabelde afbeeldingen om de nauwkeurigheid ervan te bepalen bij verschillende drempelwaarden voor betrouwbaarheid. Dit evaluatieproces wordt gebruikt om een drempelwaarde voor betrouwbaarheid te bepalen waarboven het model nauwkeurig genoeg is om pre-labels weer te geven. Het model wordt vervolgens geëvalueerd op basis van niet-gelabelde gegevens. Afbeeldingen met voorspellingen met een hogere betrouwbaarheid dan deze drempelwaarde, worden gebruikt om vooraf te labelen.

## <a name="initialize-the-data-labeling-project"></a>Het labelproject initialiseren

Nadat het labelproject is geïnitialiseerd, kunnen sommige aspecten van het project niet meer worden gewijzigd. U kunt het taaktype of de gegevensset niet wijzigen. U kunt labels en de URL voor de taakbeschrijving *wel* wijzigen. Controleer zorgvuldig de instellingen voordat u het project maakt. Nadat u het project hebt verzonden, keert u terug naar de startpagina **Gegevens labelen**, waar het project wordt weergegeven als **Initialiseren**.

> [!NOTE]
> Deze pagina wordt mogelijk niet automatisch vernieuwd. Vernieuw de pagina daarom na een korte pauze handmatig om de status van het project te zien als **Gemaakt**.

## <a name="run-and-monitor-the-project"></a>Het project uitvoeren en bewaken
Nadat u het project hebt geïnitialiseerd, wordt het gestart in Azure. Selecteer het project op de pagina **Gegevens labelen** om de projectdetails te bekijken.

Als u het project wilt onderbreken of opnieuw wilt starten, schakelt u de status **Actief** in de rechterbovenhoek in. U kunt alleen gegevens labelen wanneer het project actief is.

### <a name="dashboard"></a>Dashboard

Op het tabblad **Dashboard** wordt de voortgang van de labeltaak weergegeven.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Dashboard voor gegevens labelen":::

In het voortgangsdiagram ziet u hoeveel items zijn gelabeld en hoeveel er nog niet zijn gedaan.  Items in behandeling zijn mogelijk:

* Nog niet toegevoegd aan een taak
* Opgenomen in een taak die is toegewezen aan een labelaar maar nog niet is voltooid 
* In de wachtrij geplaatst van taken die nog moeten worden toegewezen

In de middelste sectie ziet u de wachtrij met taken die nog moeten worden toegewezen. Als door ML ondersteund labelen is uitgeschakeld, wordt in deze sectie het aantal handmatige taken weergegeven dat moet worden toegewezen. Als door ML ondersteund labelen is ingeschakeld, wordt ook het volgende weergegeven:

* Taken met geclusterde items in de wachtrij
* Taken met vooraf gelabelde items in de wachtrij

Wanneer door ML ondersteund labelen is ingeschakeld, wordt er ook een kleine voortgangsbalk weergegeven wanneer de volgende training wordt uitgevoerd.  De secties van Experimenten bevatten koppelingen voor de afzonderlijke machine learning-uitvoeringen.

* Training: hiermee een model getraind voor het voorspellen van de labels
* Validatie: hiermee wordt bepaald of de voorspelling van dit model wordt gebruikt voor het vooraf labelen van de items 
* Deductie: hiermee wordt een voorspelling uitgevoerd voor nieuwe items
* Featurization: hiermee worden items geclusterd (alleen voor projecten voor afbeeldingsclassificatie)

Aan de rechterkant wordt een verdeling weergegeven van de labels voor de taken die zijn voltooid.  Omdat een item bij sommige projecttypen meerdere labels kan hebben, is het totale aantal labels mogelijk groter dan het totale aantal items.

### <a name="data-tab"></a>Het tabblad Gegevens

Op het tabblad **Gegevens** ziet u de gegevensset en kunt u de gelabelde gegevens bekijken. Als u onjuist gelabelde gegevens ziet, selecteert u deze en kiest u **Afwijzen**. Hierdoor worden de labels verwijderd en de gegevens weer in de wachtrij Niet-gelabeld geplaatst.

### <a name="details-tab"></a>Het tabblad Details

Hier kunt u de details van uw project weergeven.  Op dit tabblad kunt u het volgende doen:

* Projectdetails en invoergegevens sets weergeven
* Incrementeel vernieuwen inschakelen
* Details weergeven van de opslagcontainer die wordt gebruikt voor het opslaan van gelabelde uitvoer in uw project
* Labels toevoegen aan uw project
* De instructies bewerken die u voor labels gebruikt
* Details bewerken van door ML ondersteund labelen, waaronder inschakelen/uitschakelen

### <a name="access-for-labelers"></a>Toegang voor labelaars

Iedereen met toegang tot uw werkruimte kan de gegevens in uw project labelen.  U kunt ook de machtigingen voor labelaars aanpassen zodat deze gebruikers toegang hebben tot de sectie voor labelen maar niet tot andere onderdelen van de werkruimte voor uw labelproject.  Zie [Toegang tot een Azure Machine Learning-werkruimte beheren](how-to-assign-roles.md) voor meer informatie en ontdek hoe u de [aangepaste rol voor labelaars](how-to-assign-roles.md#labeler) kunt maken.

## <a name="add-new-label-class-to-a-project"></a>Nieuwe labelklasse toevoegen aan een project

Tijdens het labelen kan het voorkomen dat er extra labels nodig zijn om uw afbeeldingen te classificeren.  Bijvoorbeeld omdat u een label Onbekend of Overig wilt toevoegen om verwarrende afbeeldingen aan te duiden.

Gebruik deze stappen om een of meer labels aan een project toe te voegen:

1. Selecteer het project op de pagina **Gegevens labelen**.
1. Wijzig in de rechterbovenhoek van de pagina **Actief** in **Onderbroken** om de activiteit voor labelaars stop te zetten.
1. Klik op het tabblad **Details**.
1. Selecteer in de lijst aan de linkerkant **Labelklassen**.
1. Selecteer bovenaan de lijst **+ Labels toevoegen** ![Een label toevoegen](media/how-to-create-labeling-projects/add-label.png)
1. Voeg in het formulier het nieuwe label toe, en kies hoe u wilt verdergaan.  Omdat u de beschikbare labels voor een afbeelding hebt gewijzigd, kiest u hoe de eerder gelabelde gegevens moeten worden behandeld:
    * Opnieuw beginnen en alle bestaande labels verwijderen.  Kies deze optie als u helemaal op nieuw wilt beginnen met labelen, op basis van de nieuwe volledige set labels. 
    * Opnieuw beginnen en alle bestaande labels behouden.  Kies deze optie om alle gegevens te markeren als niet-gelabeld, maar de bestaande labels te behouden als standaardtag voor afbeeldingen die eerder zijn gelabeld.
    * Doorgaan en alle bestaande labels behouden. Kies deze optie om alle eerder gelabelde inhoud ongewijzigd te laten, en vanaf nu de nieuwe labels te gebruiken voor gegevens die nog niet zijn gelabeld.
1. Wijzig de pagina met instructies voor de nieuwe labels, indien nodig.
1. Zodra u alle nieuwe labels hebt toegevoegd, wijzigt in de rechterbovenhoek van de pagina **Onderbroken** in **Actief** om het project opnieuw te starten.  

## <a name="export-the-labels"></a>De labels exporteren

U kunt de labelgegevens voor Machine Learning-experimenten op elk gewenst moment exporteren. Labels van afbeeldingen kunnen worden geëxporteerd in [COCO-indeling](http://cocodataset.org/#format-data) of als een [Azure Machine Learning-gegevensset met labels](how-to-use-labeled-dataset.md). Gebruik de knop **Exporteren** op de pagina **Projectdetails** van het labelproject.

Het COCO-bestand wordt gemaakt in de Blob-standaardopslag van de Azure Machine Learning-werkruimte in een map binnen *export/coco*. U hebt toegang tot de geëxporteerde Azure Machine Learning-gegevensset in de sectie **Gegevenssets** van Machine Learning. De pagina met details van de gegevensset biedt ook voorbeeldcode voor toegang tot uw labels vanuit Python.

![Geëxporteerde gegevens](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik deze tips als een van deze problemen optreedt.

|Probleem  |Oplossing  |
|---------|---------|
|Er kunnen alleen gegevenssets worden gebruikt die zijn gemaakt in blobgegevensopslag.     |  Dit is een bekende beperking in de huidige release.       |
|Na het maken van een project heeft het lange tijd de status Initialiseren.     | Vernieuw de pagina handmatig. De initialisatievoortgang moet ongeveer 20 gegevenspunten per seconde zijn. Het ontbreken van automatisch vernieuwen is een bekend probleem.         |
|Bij het weergeven van afbeeldingen zijn onlangs gelabelde afbeeldingen niet zichtbaar.     |   Als u alle gelabelde afbeeldingen wilt laden, kiest u de knop **Eerste**. De knop **Eerste** brengt u naar het begin van de lijst, maar alle gelabelde gegevens worden geladen.      |
|Als u op de knop Esc drukt tijdens het labelen voor objectdetectie, wordt in de linkerbovenhoek een label met grootte nul gemaakt. In dit geval mislukt het verzenden van labels.     |   Verwijder het label door op het kruis ernaast te klikken.  |
|Kan geen takenset toewijzen aan een specifieke labelaar.     |   Dit is een bekende beperking in de huidige release.  |

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Uw eerst labelproject maken voor afbeeldingsclassificatie](tutorial-labeling.md).
* Afbeeldingen labelen voor [afbeeldingsclassificatie of objectdetectie](how-to-label-images.md)
* Meer informatie over [Azure Machine Learning en Machine Learning Studio (klassiek)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
