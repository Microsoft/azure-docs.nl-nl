---
title: Preview-functies Azure Stream Analytics
description: Dit artikel bevat een overzicht van de Azure Stream Analytics-functies die momenteel als preview-versie beschikbaar zijn
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589647"
---
# <a name="azure-stream-analytics-preview-features"></a>Preview-functies Azure Stream Analytics

Dit artikel bevat een overzicht van alle functies die momenteel als preview-versie beschikbaar zijn voor Azure Stream Analytics. Het gebruik van preview-functies in een productie omgeving wordt niet aanbevolen.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Verifiëren voor SQL Database uitvoer met beheerde identiteiten (preview-versie)

Azure Stream Analytics ondersteunt [beheerde identiteits verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor Azure SQL database uitvoer-Sinks. Beheerde identiteiten elimineren de beperkingen van verificatie methoden op basis van gebruikers, zoals de nood zaak om opnieuw te verifiëren vanwege wachtwoord wijzigingen. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Beoordeling in realtime hoge prestaties met aangepaste ML-modellen die worden beheerd door Azure Machine Learning

Azure Stream Analytics ondersteunt hoogwaardige, realtime scores door gebruik te maken van aangepaste vooraf getrainde Machine Learning modellen die worden beheerd door Azure Machine Learning en die worden gehost in azure Kubernetes service (AKS) of Azure Container Instances (ACI), met behulp van een werk stroom waarvoor u geen code hoeft te schrijven. [Aanmelden](https://aka.ms/asapreview1) voor preview

## <a name="c-custom-de-serializers"></a>Aangepaste C#-serializers
Ontwikkel aars kunnen gebruikmaken van de kracht van Azure Stream Analytics om gegevens in protobuf, XML of een aangepaste indeling te verwerken. U kunt [aangepaste deserializers](custom-deserializer-examples.md) implementeren in C#, die vervolgens kunnen worden gebruikt om de gebeurtenissen te deserialiseren die door Azure stream Analytics worden ontvangen.

## <a name="extensibility-with-c-custom-code"></a>Uitbreid baarheid met aangepaste C#-code

Ontwikkel aars die Stream Analytics-modules in de Cloud of op IoT Edge maken, kunnen aangepaste C#-functies schrijven of opnieuw gebruiken en deze rechtstreeks in de query aanroepen via door de [gebruiker gedefinieerde functies](stream-analytics-edge-csharp-udf-methods.md).

## <a name="debug-query-steps-in-visual-studio"></a>Debug-query stappen in Visual Studio

U kunt eenvoudig een voor beeld van de tussenliggende Rijset in een gegevens diagram bekijken bij het uitvoeren van lokale tests in Azure Stream Analytics-hulpprogram ma's voor Visual Studio. 


## <a name="live-data-testing-in-visual-studio"></a>Live data tests in Visual Studio

Visual Studio Tools for Azure Stream Analytics verbeteren de lokale test functie waarmee u query's kunt testen op live gebeurtenis stromen vanuit Cloud bronnen zoals Event hub of IoT hub. Meer informatie over hoe u [Live-gegevens lokaal kunt testen met behulp van Azure stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio code voor Azure Stream Analytics

Azure Stream Analytics-taken kunnen worden gemaakt in Visual Studio code. Bekijk onze [zelf studie over de VS code](./quick-create-visual-studio-code.md).

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokale tests met Live-gegevens in Visual Studio code

U kunt uw query's testen op Live-gegevens op uw lokale machine voordat u de taak naar Azure verzendt. Elke test herhaling neemt gemiddeld minder dan twee tot drie seconden in beslag, wat resulteert in een zeer efficiënt ontwikkelings proces.

