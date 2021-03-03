---
title: Jupyter-notebooks uitvoeren in uw werk ruimte
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van een Jupyter-notebook zonder uw werk ruimte te verlaten in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 18ccadcf43d41c677a665ed068d093f51389b576
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657329"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Jupyter-notebooks uitvoeren in uw werk ruimte

Meer informatie over het rechtstreeks uitvoeren van uw Jupyter-notebook in uw werk ruimte in Azure Machine Learning Studio. Hoewel u [Jupyter](https://jupyter.org/) of [jjupyterlab](https://jupyterlab.readthedocs.io)kunt starten, kunt u uw notitie blokken ook bewerken en uitvoeren zonder de werk ruimte te verlaten.

Zie [bestanden in uw werk ruimte maken en beheren](how-to-manage-files.md)voor meer informatie over het maken en beheren van bestanden, met inbegrip van notitie blokken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Een notitie blok bewerken

Als u een notitie blok wilt bewerken, opent u een notitie blok dat zich bevindt in het gedeelte **gebruikers bestanden** van uw werk ruimte. Klik op de cel die u wilt bewerken.  Als u in deze sectie geen notitie blokken hebt, raadpleegt u [bestanden maken en beheren in uw werk ruimte](how-to-manage-files.md).

U kunt het notitie blok bewerken zonder verbinding te maken met een reken instantie.  Wanneer u de cellen in het notitie blok wilt uitvoeren, selecteert of maakt u een reken instantie.  Als u een gestopt Compute-exemplaar selecteert, wordt het automatisch gestart wanneer u de eerste cel uitvoert.

Wanneer een reken instantie wordt uitgevoerd, kunt u ook de voltooiing van code gebruiken, mogelijk gemaakt door [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), in een python-notebook.

U kunt Jupyter of Jjupyterlab ook starten via de werk balk van het notitie blok.  Azure Machine Learning biedt geen updates en corrigeert fouten van Jupyter of Jjupyterlab, omdat deze open source-producten zijn buiten de grenzen van Microsoft Ondersteuning.

## <a name="focus-mode"></a>Focusmodus

Gebruik de focus modus om uw huidige weer gave uit te breiden zodat u zich kunt concentreren op uw actieve tabbladen. De focus modus verbergt de bestanden Verkenner van notitie blokken.

1. Selecteer in de werk balk van het Terminal venster de **focus modus** om de focus modus in te scha kelen. Afhankelijk van de venster breedte kan dit zich bevinden onder de menu opdracht **..** . in de werk balk.
1. Ga in de focus modus terug naar de standaard weergave door de **standaard weergave** te selecteren.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Focus modus/standaard weergave in-/uitschakelen":::

## <a name="use-intellisense"></a>IntelliSense gebruiken

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) is een hulp programma voor het volt ooien van de code die een aantal functies omvat: lijst leden, parameter info, snelle informatie en volledig woord. Deze functies helpen u meer te weten te komen over de code die u gebruikt, het bijhouden van de para meters die u typt en het toevoegen van aanroepen aan eigenschappen en methoden met slechts enkele toetsaanslagen.  

Wanneer u code typt, gebruikt u Ctrl + spatie om IntelliSense te activeren.

## <a name="clean-your-notebook-preview"></a>Uw notitie blok opschonen (preview-versie)

> [!IMPORTANT]
> De functie verzamelen is momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Tijdens het maken van een notitie blok gaat u meestal naar cellen die u hebt gebruikt voor het verkennen van gegevens of het opsporen van fouten. De functie *verzamelen* helpt u bij het produceren van een schone notebook zonder deze vreemde cellen.

1. Voer alle notebook-cellen uit.
1. Selecteer de cel met de code die u voor het nieuwe notitie blok wilt uitvoeren. Bijvoorbeeld de code die een experiment verzendt of de code die een model registreert.
1. Selecteer het pictogram voor **verzamelen** dat wordt weer gegeven op de werk balk van de cel.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Scherm opname: Selecteer het pictogram verzamelen":::
1. Voer de naam in voor het nieuwe ' verzamelde ' notitie blok.  

Het nieuwe notitie blok bevat alleen code cellen, waarbij alle cellen zijn vereist voor het produceren van dezelfde resultaten als de cel die u hebt geselecteerd voor het verzamelen van gegevens.

## <a name="save-and-checkpoint-a-notebook"></a>Een notitie blok opslaan en controle punten

Azure Machine Learning een controlepunt bestand maakt wanneer u een *ipynb* -bestand maakt.

Selecteer in de werk balk notebook het menu en vervolgens **bestand &gt; opslaan en controle punt** om het notitie blok hand matig op te slaan. er wordt dan een controlepunt bestand toegevoegd dat is gekoppeld aan het notitie blok.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Scherm opname van het hulp programma opslaan op de werk balk van notitie blok":::

Elk notitie blok wordt elke 30 seconden autobespaard. Met automatisch opslaan worden alleen het eerste *ipynb* -bestand, niet het controlepunt bestand, bijgewerkt.
 
Selecteer **controle punten** in het menu van het notitie blok om een benoemd controle punt te maken en de notitie blok terug te zetten naar een opgeslagen controle punt.

## <a name="export-a-notebook"></a>Een notitie blok exporteren

Selecteer in de werk balk van het notitie blok het menu en vervolgens **exporteren als** om het notitie blok te exporteren als een van de ondersteunde typen:

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Een notitie blok exporteren naar uw computer":::

Het geëxporteerde bestand wordt op uw computer opgeslagen.

## <a name="run-a-notebook-or-python-script"></a>Een notitie blok of python-script uitvoeren

Als u een notitie blok of een python-script wilt uitvoeren, maakt u eerst verbinding met een actief [reken exemplaar](concept-compute-instance.md).

* Als u geen reken instantie hebt, gebruikt u deze stappen om er een te maken:

    1. Selecteer **+ New Compute** in de werk balk voor het notitie blok of het script rechts van de vervolg keuzelijst voor berekeningen. Afhankelijk van uw scherm grootte, kan dit zich bevinden onder een **...** -menu.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Een nieuwe Compute maken":::
    1. Geef de berekening een naam en kies een grootte voor de **virtuele machine**. 
    1. Selecteer **Maken**.
    1. Het reken proces is automatisch verbonden met het bestand.  U kunt nu de notebook-cellen of het python-script uitvoeren met het hulp programma aan de linkerkant van het reken exemplaar.

* Als u een gestopt Compute-exemplaar hebt, selecteert u  **Compute starten** rechts van de vervolg keuzelijst compute. Afhankelijk van uw scherm grootte, kan dit zich bevinden onder een **...** -menu.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Reken instantie starten":::

Alleen u kunt de compute-instanties zien en gebruiken die u maakt.  Uw **gebruikers bestanden** worden afzonderlijk van de virtuele machine opgeslagen en worden gedeeld door alle reken instanties in de werk ruimte.

### <a name="view-logs-and-output"></a>Logboeken en uitvoer weer geven

Gebruik [notebook widgets](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) om de voortgang van de uitvoering en logboeken weer te geven. Een widget is asynchroon en levert updates totdat de training is voltooid. Azure Machine Learning widgets worden ook ondersteund in Jupyter en JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Scherm opname: Jupyter notebook-widget ":::

## <a name="explore-variables-in-the-notebook"></a>Variabelen in het notitie blok verkennen

Gebruik op de werk balk van het notitie blok het hulp programma voor **variabelen Verkenner** om de naam, het type, de lengte en de voorbeeld waarden weer te geven voor alle variabelen die in uw notitie blok zijn gemaakt.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Scherm afbeelding: hulp programma voor variabele Verkenner":::

Selecteer het hulp programma om het venster van de variabele Verkenner weer te geven.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Scherm afbeelding: venster variabele Verkenner":::

## <a name="navigate-with-a-toc"></a>Navigeren met een inhouds opgave

Gebruik het hulp programma voor de  **inhouds opgave** op de werk balk van het notitie blok om de inhouds opgave weer te geven of te verbergen.  Een rij met een afkorting beginnen met een kop om deze toe te voegen aan de inhouds opgave. Klik op een item in de tabel om naar die cel in het notitie blok te scrollen.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Scherm afbeelding: inhouds opgave in het notitie blok":::

## <a name="change-the-notebook-environment"></a>De notitieblok omgeving wijzigen

Op de werk balk van notitie blokken kunt u de omgeving wijzigen waarop uw notitie blok wordt uitgevoerd.  

Met deze acties worden de status van het notitie blok of de waarden van variabelen in het notitie blok niet gewijzigd:

|Bewerking  |Resultaat  |
|---------|---------| --------|
|De kernel stoppen     |  Stopt elke actieve cel. Als u een cel uitvoert, wordt de kernel automatisch opnieuw gestart. |
|Naar een andere sectie van de werk ruimte navigeren     |     Actieve cellen worden gestopt. |

Met deze acties wordt de status van het notitie blok opnieuw ingesteld en worden alle variabelen in het notitie blok opnieuw ingesteld.

|Bewerking  |Resultaat  |
|---------|---------| --------|
| De kernel wijzigen | Notebook maakt gebruik van nieuwe kernel |
| Scha kelen tussen compute    |     In de notitie blok wordt automatisch de nieuwe Compute gebruikt. |
| Berekening opnieuw instellen | Start opnieuw wanneer u een cel probeert uit te voeren |
| Compute stoppen     |    Er worden geen cellen uitgevoerd  |
| Notitie blok openen in Jupyter of Jjupyterlab     |    Notitie blok geopend op een nieuw tabblad.  |

## <a name="add-new-kernels"></a>Nieuwe kernels toevoegen

[Gebruik de Terminal ](how-to-access-terminal.md#add-new-kernels) om nieuwe kernels te maken en toe te voegen aan uw reken exemplaar. Het notitie blok vindt automatisch alle Jupyter-kernels die zijn geïnstalleerd op het verbonden Compute-exemplaar.

Gebruik de vervolg keuzelijst kernel aan de rechter kant om een van de geïnstalleerde kernels te wijzigen.  


### <a name="status-indicators"></a>Status indicatoren

Een indicator naast de vervolg keuzelijst voor **berekeningen** toont de status.  De status wordt ook weer gegeven in de vervolg keuzelijst zelf.  

|Kleur |Compute-status |
|---------|---------| 
| Green | Compute running |
| Red |Kan niet berekenen | 
| Zwart | Berekenen gestopt |
|  Licht blauw |Berekenen maken, starten, opnieuw starten, instellen |
|  Grijs |Berekening verwijderen, stoppen |

Een indicator naast de vervolg keuzelijst **kernel** toont de status.

|Kleur |Kernel-status |
|---------|---------|
|  Green |Kernel verbonden, niet-actief, bezet|
|  Grijs |Kernel niet verbonden |

## <a name="find-compute-details"></a>Berekenings details zoeken

Meer informatie over de compute-exemplaren vindt u in [Studio](https://ml.azure.com)op de **Compute** -pagina.

## <a name="troubleshooting"></a>Problemen oplossen

* Als u geen verbinding kunt maken met een notitie blok, moet u ervoor zorgen dat de communicatie tussen websockets **niet** is uitgeschakeld. De functionaliteit van de reken instantie Jupyter werkt alleen als de WebSocket-communicatie is ingeschakeld. Zorg ervoor dat uw netwerk WebSocket-verbindingen toestaat naar *. instances.azureml.net en *. instances.azureml.ms. 

* Wanneer reken instantie wordt geïmplementeerd in een persoonlijke koppelings werkruimte, kan deze alleen worden geopend vanuit een virtueel netwerk. Als u een aangepast DNS-of hosts-bestand gebruikt, voegt u een vermelding toe voor <exemplaar naam>. <region> . instances.azureml.ms met het privé-IP-adres van het persoonlijke eind punt van de werk ruimte. Zie het [aangepaste DNS-](./how-to-custom-dns.md?tabs=azure-cli) artikel voor meer informatie.
    
## <a name="next-steps"></a>Volgende stappen

* [Uw eerste experiment uitvoeren](tutorial-1st-experiment-sdk-train.md)
* [Back-up van de bestands opslag maken met moment opnamen](../storage/files/storage-snapshots-files.md)
* [Werken in beveiligde omgevingen](./how-to-secure-training-vnet.md#compute-instance)