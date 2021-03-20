---
title: Inleiding tot Azure Stream Analytics
description: Lees over Azure Stream Analytics, een beheerde service waarmee u streaminggegevens van IoT (Internet of Things) in realtime kunt analyseren.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 11/12/2020
ms.openlocfilehash: 5aea6460f3a876d63544ce8422f9f205c22f2a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015246"
---
# <a name="welcome-to-azure-stream-analytics"></a>Welkom bij Azure Stream Analytics

Azure Stream Analytics is een realtime engine voor het analyseren en verwerken van complexe gebeurtenissen. Het is ontworpen om grote volumes van snelle streaminggegevens van verschillende bronnen tegelijkertijd te analyseren en verwerken. U kunt patronen en verbanden identificeren in informatie die wordt verkregen uit verschillende invoerbronnen waaronder apparaten, sensoren, clickstreams, socialemediafeeds en toepassingen. Deze patronen kunnen worden gebruikt als trigger voor acties of om werkstromen op te starten, bijvoorbeeld om waarschuwingen te maken, informatie door te sturen naar een rapportagetool of om getransformeerde gegevens op te slaan voor later. Daarnaast is Stream Analytics beschikbaar in Azure IoT Edge-runtime, zodat het ook gegevens op IoT-apparaten kan verwerken.

De volgende scenario's zijn voorbeelden van wanneer u Azure Stream Analytics kunt gebruiken:

* Realtime telemetriegegevens van IoT-apparaten analyseren
* Weblogboeken/clickstream-analyses op internet
* Georuimtelijke analyses voor fleetmanagement en zelfrijdende auto’s
* Op afstand bewaken en predictief onderhoud van hoogwaardige middelen
* Realtime analyse van Point of Sale-gegevens voor voorraadbeheer en anomaliedetectie

U kunt Azure Stream Analytics uitproberen met een gratis Azure-abonnement.

> [!div class="nextstepaction"]
> [Azure Stream Analytics uitproberen](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>Hoe werkt Stream Analytics?

Een Azure Stream Analytics-taak bestaat uit een invoer, query en een uitvoer. Stream Analytics haalt gegevens op uit Azure Event Hubs (waaronder Azure Event Hubs van Apache Kafka), Azure IoT Hub of Azure Blob Storage. De query, die gebaseerd is op SQL-querytaal, kan gebruikt worden om streaminggegevens gedurende een bepaalde tijd eenvoudig te filteren, sorteren, combineren en samenvoegen. U kunt deze SQL-taal ook uitbreiden met JavaScript en C#-UDF's (door gebruiker gedefinieerde functies). U kunt eenvoudig de opties voor gebeurtenisvolgorde en de duur van tijdvensters aanpassen bij het uitvoeren van combinatiebewerkingen, met behulp van eenvoudige taalconstructs en/of configuraties.

Elke taak heeft een of meer uitvoerwaarden voor de getransformeerde gegevens, en u kunt bepalen wat er gebeurt als reactie op de informatie die u hebt geanalyseerd. U kunt bijvoorbeeld:

* Stuur gegevens naar services zoals Azure Functions, Service Bus Topics of Queues om communicatie of downstream aangepaste werkstromen te activeren.
* Stuur gegevens naar een Power BI-dashboard voor realtime dashboards.
* Gegevens opslaan in andere Azure-opslagservices (bijvoorbeeld Azure Data lake, Azure Synapse Analytics, enzovoort) om een machine learning-model te trainen op basis van historische gegevens of batchanalyses uit te voeren.

In de volgende afbeelding wordt getoond hoe gegevens naar Stream Analytics worden verzonden, geanalyseerd en verder verzonden voor andere acties, zoals opslag of presentatie:

![Pijplijn voor binnenkomende gegevens voor Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Belangrijkste mogelijkheden en voordelen

Azure Stream Analytics is gebruiksvriendelijk, flexibel, betrouwbaar en schaalbaar tot elke taakgrootte. Het is beschikbaar in meerdere Azure-regio's en kan worden uitgevoerd in IoT Edge of Azure Stack.

## <a name="ease-of-getting-started"></a>Eenvoudig aan de slag

Met Azure Stream Analytics kunt u zo beginnen. Met slecht een paar klikken maakt u verbinding met meerdere bronnen en sinks om een end-to-endpijplijn te maken. Stream Analytics kan verbinding maken met Azure Event Hubs en Azure IoT Hub voor opname van streamgegevens, en met de Azure Blob-opslag voor opname van historische gegevens. Taakinvoer kan ook bestaan uit statische referentiegegevens van de Azure Blob-opslag of SQL-database die u met deze verwijzingsgegevens kunt samenvoegen om opzoekbewerkingen uit te voeren.

Stream Analytics kan taakuitvoer omleiden naar talloze opslagsystemen, bijvoorbeeld Azure Blob-opslag, Azure SQL Database, Azure Data Lake Stores en Azure Cosmos DB. U kunt ook batchanalyses uitvoeren op de uitvoer van streams met Azure Synapse Analytics of HDInsight, of u kunt de uitvoer naar een andere service verzenden, zoals Event Hubs voor verbruik of Power BI voor visualisatie in realtime.

Zie [Uitvoer van Azure Stream Analytics begrijpen](stream-analytics-define-outputs.md) voor een volledige lijst met Stream Analytics-uitvoer.

## <a name="programmer-productivity"></a>Productiviteit van programmeurs

Azure Stream Analytics maakt gebruik van een SQL-querytaal die is uitgebreid met krachtige, tijdelijke beperkingen voor het analyseren van gegevens in beweging. U kunt ook taken maken met ontwikkelaarshulpprogramma's zoals Azure PowerShell, Azure CLI, Stream Analytics Visual Studio-hulpprogramma's, de [Stream Analytics Visual Studio Code-extensie](quick-create-visual-studio-code.md), of Azure Resource Manager-sjablonen. Met ontwikkelaarstalen kunt u offline transformatiequery's ontwikkelen en de CI/CD-pijplijn gebruiken om taken bij Azure in te dienen.

Met de Stream Analytics-querytaal kunt u CEP (verwerking van complexe gebeurtenissen) uitvoeren door een breed scala aan functies te bieden voor het analyseren van streaminggegevens. Deze querytaal ondersteunt eenvoudige gegevensbewerking, -samenvoeging en analysefuncties, georuimtelijke functies, patroonherkenning en anomaliedetectie. U kunt query's in de portal bewerken of uw ontwikkelhulpprogramma's gebruiken en ze testen met voorbeeldgegevens die uit een livestream worden opgehaald.

U kunt de mogelijkheden van de querytaal uitbreiden door extra functie te definiëren en aan te roepen. In Azure Machine Learning kunt u functieaanroepen definiëren om gebruik te kunnen maken van Azure Machine Learning-oplossingen en door gebruikers gedefinieerde JavaScript- of C#-functies (UDF’s) of door gebruikers gedefinieerde aggregaten te integreren voor het uitvoeren van complexe berekeningen als onderdeel van een Stream Analytics-query.

## <a name="fully-managed"></a>Volledig beheerd

Azure Stream Analytics is een volledig beheerde (PaaS) aanbieding in Azure. U hoeft geen hardware of infrastructuur in te richten, het besturingssysteem of de software bij te werken. Azure Stream Analytics beheert uw taak volledig, zodat u zicht kunt richten op uw bedrijfslogica en niet op de infrastructuur.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>In de cloud of op de intelligente rand uitvoeren

Azure Stream Analytics kan in de cloud worden uitgevoerd voor grootschalige analysetaken, of op IoT Edge of Azure Stack worden uitgevoerd voor analyses met een zeer lage latentie. Azure Stream Analytics maakt gebruik van dezelfde tools en querytaal in zowel de cloud als op de intelligente Edge, zodat ontwikkelaars ware hybride architecturen kunnen bouwen voor het verwerken van streams. 

## <a name="low-total-cost-of-ownership"></a>Lage total cost of ownership

Als cloudservice is Stream Analytics kostenefficiënt. Er is geen sprake van opstartkosten; u betaalt slechts voor de [streaming-eenheden die u verbruikt](stream-analytics-streaming-unit-consumption.md). Er is geen verplichting en het inrichten van clusters is niet nodig. U kunt de taak omhoog of omlaag schalen in functie van uw zakelijke behoeften.

## <a name="mission-critical-ready"></a>Essentieel gereed

Azure Stream Analytics is beschikbaar in meerdere regio's over de hele wereld, en is ontworpen om uw essentiële workloads uit te voeren door ondersteuning te bieden voor de vereisten voor betrouwbaarheid, veiligheid en naleving.

### <a name="reliability"></a>Betrouwbaarheid

Azure Stream Analytics garandeert Exactly-once-verwerking van gebeurtenissen en At-least-once-levering van gebeurtenissen, zodat gebeurtenissen nooit verloren gaan. Exactly-once-verwerking is gegarandeerd met geselecteerde uitvoer, zoals beschreven in Garanties voor de levering van gebeurtenissen.

Azure Stream Analytics bevat ingebouwde herstelfuncties in geval de levering van een gebeurtenis mislukt. Stream Analytics biedt ook de ingebouwde mogelijkheid voor het gebruik van controlepunten om de status van uw taak bij te houden, en levert herhaalbare resultaten.

Als een beheerde service garandeert Stream Analytics een beschikbaarheid op minuutniveau van 99,9% voor de verwerking van gebeurtenissen. 

### <a name="security"></a>Beveiliging

Wat betreft beveiliging, versleutelt Azure Stream Analytics alle inkomende en uitgaande communicatie en biedt ondersteuning voor TLS 1.2. Ingebouwde controlepunten zijn eveneens versleuteld. Stream Analytics slaat de binnenkomende gegevens niet op omdat alle verwerking in het geheugen gebeurt. Stream Analytics biedt ook ondersteuning voor Azure Virtual Networks (VNET) bij het uitvoeren van een taak in een [Stream Analytics-cluster](./cluster-overview.md).

### <a name="compliance"></a>Naleving

Azure Stream Analytics volgt meerdere nalevingscertificeringen, zoals beschreven in het [Overzicht van Azure-naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestaties

Stream Analytics verwerkt miljoenen gebeurtenissen per seconde en kan resultaten met een ultralage latenties leveren. U kunt ermee omhoogschalen of uitschalen, zodat u intensieve toepassingen kunt afhandelen die in realtime complexe gebeurtenissen verwerken. Stream Analytics ondersteunt de hogere prestaties door gebruik te maken van partitionering, zodat complexe query's parallel en op meerdere streamingknooppunten kunnen worden uitgevoerd. Azure Stream Analytics is gebouwd op [Trill](https://github.com/Microsoft/Trill), een krachtige in-memory engine voor streaminganalyse die is ontwikkeld in samenwerking met Microsoft Research.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken met behulp van Azure Portal](stream-analytics-quick-create-portal.md)
* [Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Een Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Een Stream Analytics-taak maken met behulp van Visual Studio Code](quick-create-visual-studio-code.md)
