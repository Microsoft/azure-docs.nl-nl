---
title: Notebooks gebruiken met Azure Sentinel voor beveiligings jacht
description: In dit artikel wordt beschreven hoe u notebooks gebruikt met de Azure Sentinel-jacht-mogelijkheden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092187"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Jupyter Notebook gebruiken om beveiligingsbedreigingen op te sporen

De basis van Azure Sentinel is het gegevens archief. Het combineert hoge prestaties voor query's, dynamische schema's en schalen naar enorme gegevens volumes. De Azure Portal en alle Azure Sentinel-hulpprogram ma's gebruiken een gemeen schappelijke API voor toegang tot deze gegevens opslag. Dezelfde API is ook beschikbaar voor externe hulpprogram ma's, zoals [Jupyter](https://jupyter.org/) -notebooks en python. Hoewel veel algemene taken kunnen worden uitgevoerd in de portal, breidt Jupyter het bereik uit van wat u met deze gegevens kunt doen. Hiermee wordt volledige programmeer baarheid gecombineerd met een enorme verzameling bibliotheken voor machine learning, visualisatie en gegevens analyse. Deze kenmerken maken het Jupyter een fascinerend hulp programma voor beveiligings onderzoek en jacht.

![voor beeld van notebook](./media/notebooks/sentinel-notebooks-map.png)

We hebben de Jupyter-ervaring geïntegreerd in de Azure Portal, waardoor u eenvoudig notitie blokken kunt maken en uitvoeren om uw gegevens te analyseren. De *Kqlmagic* -bibliotheek biedt de lijm waarmee u query's kunt uitvoeren vanuit Azure Sentinel en deze rechtstreeks in een notebook uitvoert. Query's gebruiken de [Kusto-query taal](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Verschillende notebooks, ontwikkeld door enkele van de beveiligings analisten van micro soft, worden geleverd met Azure Sentinel. Sommige van deze notitie blokken zijn gebouwd voor een specifiek scenario en kunnen worden gebruikt als-is. Andere zijn bedoeld als voor beelden voor het illustreren van technieken en functies die u kunt kopiëren of aanpassen voor gebruik in uw eigen notitie blokken. Andere notitie blokken kunnen ook worden geïmporteerd vanuit de Azure Sentinel Community GitHub.

De geïntegreerde Jupyter-ervaring maakt gebruik van [Azure notebooks](https://notebooks.azure.com/) om notebooks op te slaan, te delen en uit te voeren. U kunt deze notitie blokken ook lokaal uitvoeren als u een python-omgeving hebt en Jupyter op uw computer of in andere JupterHub-omgevingen, zoals Azure Databricks.

Notebooks hebben twee onderdelen:

- De browser interface waar u query's en code invoert en uitvoert, en waar de resultaten van de uitvoering worden weer gegeven.
- Een *kernel* die verantwoordelijk is voor het parseren en uitvoeren van de code zelf.

De kernel van Azure Sentinel notebook wordt uitgevoerd op een virtuele Azure-machine (VM). Er zijn verschillende licentie opties voor het gebruik van krachtigere virtuele machines als uw Notebooks complexe machine learning modellen bevatten.

De Azure Sentinel-notebooks gebruiken veel populaire python-Bibliotheken, zoals Panda, matplotlib, bokeh en anderen. Er zijn een groot aantal andere Python-pakketten waaruit u kunt kiezen. Dit omvat gebieden zoals:

- Visualisaties en afbeeldingen
- Gegevens verwerking en-analyse
- Statistieken en numerieke computing
- Machine learning en deep learning

Daarnaast hebben we een aantal open-source Jupyter-beveiligings hulpprogramma's uitgebracht in een pakket met de naam [msticpy](https://github.com/Microsoft/msticpy/). Dit pakket wordt gebruikt in veel van de meegeleverde notitie blokken. Msticpy-hulpprogram ma's zijn speciaal ontworpen voor het maken van notitie blokken voor jacht en onderzoek en we werken op dit moment met nieuwe functies en verbeteringen.

De [Azure Sentinel Community github-opslag plaats](https://github.com/Azure/Azure-Sentinel) is de locatie voor toekomstige Azure-Sentinel-notebooks die door micro soft zijn gebouwd of die zijn bijgedragen van de community.

Als u de notitie blokken wilt gebruiken, moet u eerst een Azure Machine Learning (ML)-werk ruimte maken.

## <a name="create-an-azure-ml-workspace"></a>Een Azure ML-werk ruimte maken

1. Navigeer vanuit het Azure Portal naar **Azure Sentinel**  >  **Threat Management**-  >  **notebooks** en selecteer vervolgens **notebook starten**.

    > [!div class="mx-imgBorder"]
    > ![notitie blok starten om Azure ml-werk ruimte te starten](./media/notebooks/sentinel-notebooks-launch.png)

1. Selecteer in de **werk ruimte AzureML** de optie **nieuwe maken**.

    > [!div class="mx-imgBorder"]
    > ![werk ruimte maken](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Geef op de pagina **machine learning** de volgende informatie op en selecteer vervolgens **controleren + maken**.

    |Veld|Beschrijving|
    |--|--|
    |Abonnement|Selecteer het Azure-abonnement dat u wilt gebruiken.|
    |Resourcegroep|Gebruik een bestaande resourcegroep in uw abonnement of voer een naam in om een nieuwe resourcegroep te maken. Een resourcegroep bevat gerelateerde resources voor een Azure-oplossing. In dit voor beeld gebruiken we **AzureMLRG**.|
    |Werkruimtenaam|Voer een unieke naam in die uw werkruimte aanduidt. In dit voor beeld gebruiken we **testworkspace1**. Namen moeten uniek zijn binnen de resourcegroep. Gebruik een naam die gemakkelijk te onthouden is en te onderscheiden is van door anderen gemaakte werkruimten.|
    |Region|Selecteer de locatie die zich het dichtst bij uw gebruikers en de gegevensresources bevindt om uw werkruimte te maken.|
    |Werkruimte-editie|Selecteer **Basic** als het type werk ruimte in dit voor beeld. Het type werk ruimte (Basic & Enter prise) bepaalt de functies waartoe u toegang hebt en de prijzen.|

    > [!div class="mx-imgBorder"]
    > ![Details van de werk ruimte opgeven](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Controleer de informatie, Controleer of deze juist is en selecteer vervolgens **maken** om de implementatie van uw werk ruimte te starten.

    > [!div class="mx-imgBorder"]
    > ![Details van de werk ruimte bekijken](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Het kan enkele minuten duren om uw werk ruimte in de cloud te maken, op het moment dat de **overzichts** pagina de huidige implementatie status weergeeft.

    > [!div class="mx-imgBorder"]
    > ![implementatie van werk ruimte](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Zodra de implementatie is voltooid, kunt u notitie blokken in uw nieuwe Azure ML-werk ruimte starten.

> [!div class="mx-imgBorder"]
> ![implementatie van werk ruimte voltooid](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Een notitie blok starten met behulp van uw Azure ML-werk ruimte

1. Ga vanuit het Azure Portal naar **Azure Sentinel**  >  **Threat Management**  >  -**notebooks**, waar u notitie blokken kunt zien die Azure Sentinel biedt.

    > [!TIP]
    > Selecteer **hulp lijnen & feedback** om een deel venster te openen met aanvullende hulp en richt lijnen voor notebooks.
    > ![Notebook richtlijnen weer geven](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Selecteer afzonderlijke notitie blokken om de beschrijvingen, vereiste gegevens typen en gegevens bronnen weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Details van notitie blok weer geven](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Selecteer het notitie blok dat u wilt gebruiken en selecteer vervolgens **notebook starten** om het notitie blok te klonen en te configureren in een nieuw Azure notebooks project dat verbinding maakt met uw Azure Sentinel-werk ruimte. Wanneer het proces is voltooid, wordt het notitie blok in Azure Notebooks geopend, zodat u het kunt uitvoeren.

    > [!div class="mx-imgBorder"]
    > ![notitie Blok selecteren](./media/notebooks/sentinel-azure-notebooks-select.png)

1. Selecteer in de werk ruimte AzureML de Azure ML-werk ruimte en selecteer vervolgens **starten**.

    > [!div class="mx-imgBorder"]
    > ![notitie blok starten](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Selecteer een reken instantie. Als u geen reken instantie hebt, gaat u als volgt te werk:
    1. Selecteer het plus teken (+) om de wizard **Nieuw reken exemplaar** te starten.

        > [!div class="mx-imgBorder"]
        > ![wizard reken instantie starten](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Geef op de pagina **Nieuw reken** proces de vereiste gegevens op en selecteer vervolgens **maken**.

        > [!div class="mx-imgBorder"]
        > ![Reken instantie maken](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Zodra uw notebook server is gemaakt, selecteert u in elke cel het pictogram uitvoeren om code in de notitie blokken uit te voeren.

    > [!div class="mx-imgBorder"]
    > ![notitie blok uitvoeren](./media/notebooks/sentinel-azure-notebooks-run.png)

Aanbevelingen:

- Zie de hand leiding aan de slag [met Azure ml-notebooks en Azure-Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) voor een snelle inleiding in het opvragen van gegevens in azure Sentinel.

- U vindt aanvullende voor beelden van notitie blokken in de submap voor [**beeld-notebook**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) github. Deze voorbeeld notitieblokken zijn met gegevens opgeslagen, zodat de beoogde uitvoer gemakkelijker te zien is. U wordt aangeraden deze notitie blokken weer te geven in [nbviewer](https://nbviewer.jupyter.org/).

- De [**HOWTOs**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) github-submap bevat notebooks, zoals het instellen van de standaard Python-versie, het configureren van een DSVM, het maken van Azure Sentinel-blad wijzers van een notitie blok en andere onderwerpen.

De meegeleverde notebooks zijn bedoeld als zowel nuttige hulpprogram ma's als illustraties en code voorbeelden die u kunt gebruiken in de ontwikkeling van uw eigen notitie blokken.

We geven feedback, of suggesties, aanvragen voor functies, bijgedragen notitie blokken, fout rapporten of verbeteringen en toevoegingen aan bestaande notitie blokken. Ga naar de [Azure Sentinel Community github](https://github.com/Azure/Azure-Sentinel) om een probleem op te lossen of Fork te maken en een bijdrage te uploaden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aan de slag kunt gaan met Jupyter Notebook in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief zoeken naar bedreigingen](hunting.md)
- [Gebruik blad wijzers voor het opslaan van interessante informatie tijdens het jacht](bookmarks.md)
