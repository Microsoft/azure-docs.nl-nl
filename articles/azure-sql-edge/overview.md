---
title: Wat is Azure SQL Edge?
description: Meer informatie over Azure SQL Edge
keywords: Inleiding tot SQL Edge, wat is SQL Edge, overzicht van SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395067"
---
# <a name="what-is-azure-sql-edge"></a>Wat is Azure SQL Edge?

Azure SQL Edge is een geoptimaliseerde relationele data base-engine die is toegespitst op IoT en IoT Edge implementaties. Het biedt mogelijkheden voor het maken van een hoogwaardige gegevens opslag en verwerkings laag voor IoT-toepassingen en-oplossingen. Azure SQL Edge biedt mogelijkheden voor het streamen, verwerken en analyseren van relationele en niet-relationele gegevens, zoals JSON, Graph en time-series, waardoor deze de juiste keuze is voor een groot aantal moderne IoT-toepassingen.

Azure SQL Edge is gebaseerd op de nieuwste versies van de [SQL server data base-engine](/sql/sql-server/sql-server-technical-documentation), waarmee toonaangevende prestaties, beveiligings-en query verwerkings mogelijkheden worden geboden. Omdat Azure SQL Edge is gebouwd op dezelfde engine als [SQL Server](/sql/sql-server/sql-server-technical-documentation) en [Azure SQL](../azure-sql/index.yml), levert deze dezelfde Transact-SQL (T-sql)-programmeer Surface Area die het ontwikkelen van toepassingen of oplossingen eenvoudiger en sneller maakt, en zorgt u ervoor dat toepassings portabiliteit tussen IOT edge apparaten, data centers en de Cloud direct kan worden uitgevoerd.

Wat is Azure SQL Edge video op Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Implementatie modellen

Azure SQL Edge ondersteunt twee implementatie modi.

- Verbonden implementatie via Azure IoT Edge: Azure SQL Edge is beschikbaar op de Azure Marketplace en kan worden geïmplementeerd als een module voor [Azure IOT Edge](../iot-edge/about-iot-edge.md). Zie [Azure SQL Edge implementeren](deploy-portal.md)voor meer informatie.<br>

![Diagram van overzicht van SQL-rand](media/overview/overview.png)

- Niet-verbonden implementatie: Azure SQL Edge-container installatie kopieën kunnen worden opgehaald uit de docker-hub en worden geïmplementeerd als een zelfstandige docker-container of een kubernetes-cluster. Zie [Azure SQL Edge implementeren met docker](disconnected-deployment.md) en [een Azure SQL Edge-container implementeren in Kubernetes](deploy-kubernetes.md)voor meer informatie.

## <a name="editions-of-sql-edge"></a>Edities van SQL-rand

SQL Edge is beschikbaar in twee verschillende edities of software plannen. Deze edities hebben identieke functie sets en verschillen alleen met de gebruiks rechten en de hoeveelheid CPU/geheugen die ze ondersteunen.

   |**Plannen**  |**Beschrijving**  |
   |---------|---------|
   |Ontwikkel aars van Azure SQL Edge  |  De SKU van alleen Development, elke SQL Edge-container is beperkt tot Maxi maal 4 kernen en 32 GB geheugen  |
   |Azure SQL Edge    |  Productie-SKU, elke SQL Edge-container is beperkt tot Maxi maal 8 kernen en 64 GB geheugen. |

## <a name="pricing-and-availability"></a>Prijzen en beschik baarheid

Azure SQL Edge is nu algemeen beschikbaar. Zie [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/)(Engelstalig) voor meer informatie over de prijzen en beschik baarheid in bepaalde regio's.

> [!IMPORTANT]
> Zie [ondersteunde functies van Azure SQL Edge](features.md)voor meer informatie over de functie verschillen tussen Azure SQL edge en SQL Server, evenals de verschillen tussen de verschillende opties van de Azure SQL-rand.

## <a name="streaming-capabilities"></a>Streaming-mogelijkheden  

Azure SQL Edge biedt ingebouwde streaming-mogelijkheden voor realtime analyses en complexe gebeurtenis verwerking. De streaming-mogelijkheid is gebouwd op basis van dezelfde constructies als [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) en vergelijk bare mogelijkheden als [Azure stream Analytics op IOT Edge](../stream-analytics/stream-analytics-edge.md).

De Streaming-Engine voor Azure SQL Edge is ontworpen voor lage latentie, tolerantie, efficiënt gebruik van band breedte en naleving. 

Raadpleeg voor meer informatie over het streamen van gegevens in SQL-Edge [data streaming](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Mogelijkheden voor Machine Learning en kunst matige intelligentie

Azure SQL Edge biedt ingebouwde machine learning-en analyse mogelijkheden door de ONNX open format te integreren (open Neural Network Exchange), waarmee diepe leren en Neural-netwerk modellen tussen verschillende Frameworks kunnen worden uitgewisseld. Zie [hier](https://onnx.ai/)voor meer informatie over ONNX. ONNX runtime biedt de flexibiliteit voor het ontwikkelen van modellen in een taal of hulpprogram ma's van uw keuze, die vervolgens kunnen worden geconverteerd naar de ONNX-indeling voor uitvoering binnen SQL Edge. Zie [machine learning en kunst matige intelligentie met ONNX in SQL Edge](onnx-overview.md)voor meer informatie.

## <a name="working-with-azure-sql-edge"></a>Werken met Azure SQL Edge

Azure SQL Edge maakt het ontwikkelen en onderhouden van toepassingen eenvoudiger en productiever. Gebruikers kunnen alle vertrouwde hulpprogram ma's en vaardig heden gebruiken om geweldige apps en oplossingen te bouwen voor hun IoT Edge behoeften. Gebruiker kan in SQL Edge ontwikkelen met hulp middelen als de volgende:

- [De Azure Portal](https://portal.azure.com/) -een webtoepassing voor het beheren van alle Azure-Services.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) : een gratis, Download bare client toepassing voor het beheren van een SQL-infra structuur, van SQL Server tot SQL database.
- [SQL Server Data tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) : een gratis, Download bare client toepassing voor het ontwikkelen van SQL Server relationele data bases, SQL-data bases, integratie Services-pakketten, Analysis Services gegevens modellen en Reporting Services-rapporten.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) : een gratis, Download bare, data base tool voor meerdere platforms voor data Professional met behulp van de micro soft-familie van on-premises en Cloud gegevens platformen in Windows, MacOS en Linux.
- [Visual Studio code](https://code.visualstudio.com/docs) : een gratis, Download bare, open-source code-editor voor Windows, MacOS en Linux. Het ondersteunt extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's Microsoft SQL Server, Azure SQL database en Azure Synapse Analytics.


## <a name="next-steps"></a>Volgende stappen

- [SQL-rand via Azure Portal implementeren](deploy-portal.md)
- [Machine Learning en kunst matige intelligentie met SQL Edge](onnx-overview.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge](tutorial-deploy-azure-resources.md)