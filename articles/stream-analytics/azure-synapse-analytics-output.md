---
title: Azure Synapse Analytics-uitvoer van Azure Stream Analytics
description: In dit artikel wordt Azure Synapse Analytics beschreven als uitvoer voor Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 7e85df8ae67624a253a9fb617629d7355109c210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019598"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure Synapse Analytics-uitvoer van Azure Stream Analytics

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) is een oneindig analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd. 

Azure Stream Analytics-taken kunnen worden uitgevoerd naar een specifieke SQL-groeps tabel in azure Synapse Analytics en kunnen doorvoer snelheden tot 200 MB per seconde verwerken. Dit biedt ondersteuning voor de meest veeleisende, realtime analyse-en hot-path gegevens verwerking voor werk belastingen, zoals rapportage en dash boarding.  

De toegewezen SQL-groeps tabel moet bestaan voordat u deze als uitvoer kunt toevoegen aan uw Stream Analytics-taak. Het schema van de tabel moet overeenkomen met de velden en hun typen in de uitvoer van uw taak. 

Als u Azure Synapse als uitvoer wilt gebruiken, moet u ervoor zorgen dat u het opslag account hebt geconfigureerd. Navigeer naar de instellingen van het opslag account om het opslag account te configureren. Alleen de typen opslag accounts die ondersteuning bieden voor tabellen zijn toegestaan: v2 en algemeen gebruik v1. Selecteer alleen standaard laag. De Premium-laag wordt niet ondersteund.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van de Azure Synapse Analytics-uitvoer.

|Naam van eigenschap|Description|
|-|-|
|Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze data base te sturen. |
|Database |de naam van de exclusieve SQL-groep waarnaar u uw uitvoer verzendt. |
|Servernaam |Naam van de Azure Synapse-server.  |
|Gebruikersnaam |De gebruikers naam die schrijf toegang tot de data base heeft. Stream Analytics ondersteunt alleen SQL-verificatie. |
|Wachtwoord |Het wacht woord om verbinding te maken met de data base. |
|Tabel  | De tabel naam waarin de uitvoer wordt geschreven. De tabel naam is hoofdletter gevoelig. Het schema van deze tabel moet exact overeenkomen met het aantal velden en de bijbehorende typen die uw taak uitvoer genereert.|

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database of Azure Synapse Analytics vanuit een Azure Stream Analytics-taak (preview)](sql-database-output-managed-identity.md)
* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)