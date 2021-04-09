---
title: 'Zelfstudie: Gedetailleerd overzicht van Machine Learning op Azure IoT Edge'
description: Een hoogwaardige zelfstudie die de verschillende taken doorloopt die nodig zijn om een end-to-end-, machine learning aan de rand-scenario te maken.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67cc470b4f7f119b7f5b86bcb82ea284ab662dfe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463235"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Zelfstudie: Een end-to-end oplossing met behulp van Azure Machine Learning en IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Vaak willen IoT-toepassingen profiteren van de intelligente cloud en de intelligente rand van het netwerk. In deze zelfstudie wordt u begeleid bij het trainen van een machine learning-model met gegevens die zijn verzameld van IoT-apparaten in de cloud, waarbij u dat model implementeert naar IoT Edge en het model regelmatig onderhoudt en verfijnt.

>[!NOTE]
>De concepten in deze set zelf studies zijn van toepassing op alle versies van IoT Edge, maar het voorbeeld apparaat dat u maakt om het scenario uit te proberen, wordt uitgevoerd IoT Edge versie 1,1.

Het belangrijkste doel van deze zelfstudie is het introduceren van de verwerking van IoT-gegevens met machine learning, met name aan de rand. Hoewel we veel aspecten van een algemene machine learning-werkstroom behandelen, is deze zelfstudie niet bedoeld als een diepgaande inleiding tot machine learning. Als voorbeeld wordt niet geprobeerd een model met hoge betrouwbaarheid te maken voor de use-case. We doen net genoeg om het proces van het maken en gebruiken van een levensvatbaar model voor IoT-gegevensverwerking te illustreren.

In deze sectie van de zelfstudie wordt het volgende besproken:

> [!div class="checklist"]
>
> * De vereisten voor het voltooien van de volgende onderdelen van de zelfstudie.
> * De doelgroep van de zelfstudie.
> * Het gebruiksvoorbeeld dat in de zelfstudie wordt gesimuleerd.
> * Het algemene proces dat de zelfstudie volgt om te voldoen aan het gebruiksvoorbeeld.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u de zelfstudie wilt voltooien, moet u toegang hebben tot een Azure-abonnement waarin u de benodigde rechten hebt om resources te maken. Voor diverse services die in deze zelfstudie worden gebruikt, worden Azure-kosten in rekening gebracht. Als u nog geen Azure-abonnement hebt, kunt u aan de slag met een [Gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/).

U hebt ook een machine nodig waarop PowerShell is geïnstalleerd, waar u scripts kunt uitvoeren om een virtuele Azure-machine in te stellen als uw ontwikkelmachine.

In dit document gebruiken we de volgende set hulpprogramma's:

* Een Azure IoT-hub voor het vastleggen van gegevens

* Azure Notebooks als de belangrijkste front-end voor het voorbereiden van gegevens en machine learning-experimenten. Het uitvoeren van python-code in een notebook op een subset van de voorbeeldgegevens is een uitstekende manier om snel iteratieve en interactieve verwerkingstijd te verkrijgen tijdens de voorbereiding van gegevens. Jupyter-notebooks kunnen ook worden gebruikt om scripts voor te bereiden die op schaal worden uitgevoerd in een berekenings-back-end.

* Azure Machine Learning als back-end voor machine learning op schaal en voor het genereren van machine learning-installatiekopieën. We sturen de Azure Machine Learning back-end met behulp van scripts die zijn voorbereid en getest in Jupyter-notebooks.

* Azure IoT Edge voor een off-Cloud toepassing van een machine learning-installatiekopie

Er zijn echter ook andere opties beschikbaar. In bepaalde scenario's kan IoT Central bijvoorbeeld worden gebruikt als alternatief voor het vastleggen van de eerste trainingsgegevens van IoT-apparaten.

## <a name="target-audience-and-roles"></a>Doelgroep en rollen

Deze set artikelen is bedoeld voor ontwikkelaars zonder eerdere ervaring in IoT-ontwikkeling of machine learning. Voor de implementatie van machine learning aan de rand is kennis nodig van het aansluiten van een breed scala aan technologieën. Deze zelfstudie bevat daarom een volledig end-to-end-scenario om te laten zien hoe u deze technologieën samen kunt koppelen aan een IoT-oplossing. In een praktijkomgeving kunnen deze taken worden verdeeld over verschillende personen met verschillende specials. Ontwikkelaars richten zich bijvoorbeeld op de apparaat- of de cloudcode, terwijl gegevenswetenschappers de analysemodellen hebben ontworpen. Om ervoor te zorgen dat een individuele ontwikkelaar deze zelfstudie kan voltooien, hebben we aanvullende richtlijnen gegeven met inzichten en koppelingen naar meer informatie die we nodig hebben om te begrijpen wat er gebeurt, en waarom.

U kunt ook samenwerken met collega's van verschillende rollen om de zelfstudie te volgen, uw volledige expertise over te brengen en als een team te leren hoe zaken in elkaar passen.

In beide gevallen wordt in elk artikel in deze zelfstudie de rol van de gebruiker weergegeven om te helpen bij de oriëntatie van de lezer(s). Deze rollen zijn onder andere:

* Cloudontwikkeling (inclusief een cloudontwikkelaar die werkt in een DevOps-capaciteit)
* Gegevensanalyse

## <a name="use-case-predictive-maintenance"></a>Toepassing: Voorspellend onderhoud

We hebben dit scenario gebaseerd op een gebruiksvoorbeeld dat wordt gepresenteerd in de conferentie over Prognostics and Health Management (PHM08) in 2008. Het doel is om de resterende nuttige levensduur (RUL) van een set turbofan-vliegtuigmotoren te voorspellen. Deze gegevens zijn gegenereerd met C-MAPSS, de commerciële versie van MAPSS (Modular Aero-Propulsion System Simulation)-software. Deze software biedt een flexibele turbofan-engine simulatieomgeving waarmee u de status-, controle- en motorparameters eenvoudig kunt simuleren.

De gegevens die in deze zelfstudie worden gebruikt, zijn afkomstig uit de [Simulatie gegevensverzameling voor turbofan-motoren](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

In het Leesmij-bestand:

***Experimenteel scenario***

*Gegevenssets bestaan uit meerdere multidimensionale tijdreeksen. Elke gegevensset wordt verder onderverdeeld in trainings- en testsubsets. Elke tijdreeks is afkomstig van een andere motor, dat wil zeggen dat de gegevens kunnen worden beschouwd als een vloot van motoren van hetzelfde type. Elke motor begint met een afwijkende mate van eerste slijtage en productievariaties die voor de gebruiker onbekend zijn. Deze slijtage en variant worden als normaal beschouwd, dat wil zeggen, niet als een probleemvoorwaarde. Er zijn drie operationele instellingen die een aanzienlijk effect hebben op de prestaties van de motor. Deze instellingen zijn ook opgenomen in de gegevens. De gegevens worden verontreinigd met sensorruis.*

*De motor werkt normaal aan het begin van elke tijdreeks en ontwikkelt op een bepaald moment tijdens de serie een fout. In de Trainingsset wordt de fout in omvang vergroot tot systeemstoringen. In de testset eindigt de tijdserie enige tijd voordat de systeemfout optreedt. Het doel van de competitie is het voorspellen van het aantal resterende operationele cycli vóór het mislukken van de testset, d.w.z. het aantal operationele cycli na de laatste cyclus dat de motor actief blijft. Daarnaast hebt u ook een vectorgegeven van de resterende levensduur (RUL)-waarden voor de testgegevens.*

Omdat de gegevens zijn gepubliceerd voor een competitie, zijn verschillende benaderingen voor het afleiden van machine learning-modellen onafhankelijk gepubliceerd. We hebben vastgesteld dat de studievoorbeelden nuttig zijn voor het begrijpen van het proces en de redenering die betrokken zijn bij het maken van een specifiek machine learning-model. Zie bijvoorbeeld:

[Voorspellingsmodel voor het uitvallen van een vliegtuigmotor](https://github.com/jancervenka/turbofan_failure) door GitHub-gebruiker jancervenka.

[Degradatie van de turbofan-motor](https://github.com/hankroark/Turbofan-Engine-Degradation) door GitHub-gebruiker hankroark.

## <a name="process"></a>Proces

In de onderstaande afbeelding ziet u de ruwe stappen die we in deze zelfstudie volgen:

![Architectuurdiagram voor processtappen](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Trainingsgegevens verzamelen**: Het proces begint met het verzamelen van trainingsgegevens. In sommige gevallen zijn gegevens al verzameld en beschikbaar in een database, of in de vorm van gegevensbestanden. In andere gevallen, met name voor IoT-scenario's, moeten de gegevens worden verzameld van IoT-apparaten en sensoren en worden opgeslagen in de cloud.

   We gaan ervan uit dat u geen verzameling turbofan-motoren hebt, zodat de projectbestanden een eenvoudige apparaatsimulator bevatten die de gegevens van de NASA-apparaten naar de cloud verzendt.

1. **Gegevens voorbereiden**. In de meeste gevallen vereisen de onbewerkte gegevens die worden verzameld van apparaten en sensoren, voorbereiding voor machine learning. Deze stap kan het opschonen van gegevens, het opnieuw formatteren van gegevens of voorverwerking omvatten om extra informatie te injecteren die machine learning kan worden uitgeschakeld.

   Voor de machinegegevens van onze vliegtuigmotor moet gegevensvoorbereiding een expliciete time-to-failure tijd berekenen voor elk gegevenspunt in het voorbeeld op basis van de werkelijke waarnemingen van de gegevens. Met deze informatie kan het machine learning-algoritme correlaties tussen de werkelijke patronen van sensorgegevens en de verwachte resterende levensduur van de motor vinden. Deze stap is zeer specifiek voor een domein.

1. **Een machine learning-model maken**. Op basis van de voorbereide gegevens kunnen we nu experimenteren met verschillende machine learning-algoritmen en parameterisaties om modellen te trainen en de resultaten met elkaar te vergelijken.

   In dit geval vergelijken we het voorspelde resultaat dat door het model is berekend met het reële resultaat dat is waargenomen voor een set motoren. In Azure Machine Learning kunnen we de verschillende iteraties van modellen die we in een modelregister maken, beheren.

1. **Het model implementeren**. Zodra we een model hebben dat aan onze criteria voor succes voldoet, kunnen we overstappen op de implementatie. Dit omvat het model inpakken in een webservice-app die kan worden ingevoerd met gegevens met behulp van REST-aanroepen en analyseresultaten retourneren. De web service-app wordt vervolgens ingepakt in een docker-container, die op zijn beurt kan worden geïmplementeerd in de cloud of als een IoT Edge-module. In dit voorbeeld is de implementatie gericht op IoT Edge.

1. **Het model onderhouden en verfijnen**. Het werk wordt niet klaar wanneer het model is geïmplementeerd. In veel gevallen willen we doorgaan met het verzamelen van gegevens en deze gegevens periodiek uploaden naar de cloud. We kunnen deze gegevens vervolgens gebruiken om ons model opnieuw te trainen en te verfijnen. Dit kan vervolgens opnieuw worden geïmplementeerd voor IoT Edge.

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht met het opschonen van resources totdat u de laatste zelfstudie hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Deze zelfstudie is onderverdeeld in de volgende secties:

1. Ontwikkelcomputer en Azure-services instellen.
2. De trainingsgegevens voor de module machine learning genereren.
3. Een Azure Machine Learning-model trainen en implementeren.
4. Een IoT Edge-apparaat configureren zodat dit werkt als een transparante gateway.
5. IoT Edge-modules maken en implementeren.
6. Meld u aan bij uw IoT Edge-apparaat.

Ga door naar het volgende artikel om een ontwikkelmachine in te stellen en Azure-resources in te richten.

> [!div class="nextstepaction"]
> [Een omgeving instellen voor machine learning op IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)