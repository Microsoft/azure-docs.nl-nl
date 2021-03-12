---
title: Data base-instellingen configureren in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u de data base-instellingen in azure API kunt configureren voor FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3ce51141f20d2e032cb255f53efe3cb63882f1b2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018099"
---
# <a name="configure-database-settings"></a>Database-instellingen configureren 

De Azure-API voor FHIR gebruikt data base om de gegevens op te slaan. De prestaties van de onderliggende Data Base zijn afhankelijk van het aantal aanvraag eenheden (RU) dat is geselecteerd tijdens het inrichten van services of in de data base-instellingen nadat de service is ingericht.

Azure API voor FHIR leent het concept van RUs van Cosmos DB (Zie [aanvraag eenheden in azure Cosmos DB](../../cosmos-db/request-units.md)) bij het instellen van de prestaties van de onderliggende data base. 

De door voer moet worden ingericht om ervoor te zorgen dat er te allen tijde voldoende systeem bronnen beschikbaar zijn voor uw data base. Hoeveel RUs u nodig hebt voor uw toepassing is afhankelijk van de bewerkingen die u uitvoert. Bewerkingen kunnen variëren van eenvoudige Lees-en schrijf bewerkingen naar complexere query's. 

> [!NOTE]
> Als verschillende bewerkingen een verschillend aantal RU gebruiken, retour neren we het werkelijke aantal dat is gebruikt in elke API-aanroep in de reactie header. Op deze manier kunt u het aantal door uw toepassing verbruikte RUs-profielen.

## <a name="update-throughput"></a>Doorvoer bijwerken

Als u deze instelling in de Azure Portal wilt wijzigen, gaat u naar de Azure API voor FHIR en opent u de Blade data base. Wijzig vervolgens de ingerichte door voer naar de gewenste waarde, afhankelijk van uw prestatie behoeften. U kunt de waarde wijzigen tot een maximum van 10.000 RU/s. Als u een hogere waarde nodig hebt, neemt u contact op met de ondersteuning van Azure.

Als de doorvoer capaciteit van de data base groter is dan 10.000 RU/s of als de gegevens die in de Data Base zijn opgeslagen meer dan 50 GB zijn, moet uw client toepassing vervolg tokens kunnen verwerken. Er wordt in de Data Base een nieuwe partitie gemaakt voor elke doorvoer verhoging van 10.000 RU/s of als de hoeveelheid opgeslagen gegevens groter is dan 50 GB. Meerdere partities maken een antwoord voor meerdere pagina's waarbij paginering wordt geïmplementeerd met behulp van vervolg tokens.

> [!NOTE] 
> Hoe hoger de waarde, hoe hoger de Azure-API voor FHIR-door Voer en hogere kosten voor de service.

![Cosmos DB configuratie](media/database/database-settings.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de RUs voor Azure API voor FHIR bijwerkt. Meer informatie over het configureren van door de klant beheerde sleutels als een database instelling:

>[!div class="nextstepaction"]
>[Door klant beheerde sleutels configureren](customer-managed-key.md)

U kunt ook een volledig beheerde Azure API implementeren voor FHIR:
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)