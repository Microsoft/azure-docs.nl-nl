---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121597"
---
| Domeinnamen                  | Uitgaande poorten | Beschrijving                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Wordt gebruikt door de zelf-hostende Integration Runtime om verbinding te maken met het Azure-opslagaccount wanneer u de functie voor gefaseerd kopiëren gebruikt. |
| `*.database.windows.net`      | 1433           | Alleen vereist bij het kopiëren van of naar Azure SQL Database of Azure Synapse Analytics, en anders optioneel. Gebruik de functie voor gefaseerd kopiëren om gegevens te kopiëren naar SQL Database of Azure Synapse Analytics zonder poort 1433 te openen. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Alleen vereist als u kopieert van of naar Azure Data Lake Store, en anders optioneel. |
