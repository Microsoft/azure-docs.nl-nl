---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fcfe05db6a9be1049ca5da06985f31135ac79f3b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555065"
---
U wordt gefactureerd voor Azure Storage op basis van het gebruik van uw opslagaccount. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. 

Opslagkosten worden berekend op basis van de volgende factoren: 

* **Regio** verwijst naar de geografische regio waarin uw account is gebaseerd.
* **Accounttype** verwijst naar het type opslagaccount dat u gebruikt. 
* **Toegangslaag** verwijst naar het gegevensgebruikpatroon dat u hebt opgegeven voor het v2- of blob-opslagaccount voor algemeen gebruik.
* **Opslagcapaciteit** verwijst naar welk aandeel van uw opslagaccount u gebruikt voor het opslaan van gegevens.
* **Replicatie** bepaalt hoeveel kopieën van uw gegevens gelijktijdig worden bewaard en op welke locaties.
* **Transacties** verwijst naar alle lees- en schrijfbewerkingen naar Azure Storage.
* **Uitgaande gegevens** verwijst naar alle gegevens die buiten een Azure-regio zijn overgedragen. Wanneer de gegevens in uw opslagaccount worden geopend door een toepassing die niet wordt uitgevoerd in dezelfde regio, wordt er een bedrag in rekening gebracht voor uitgaande gegevens. Zie [Wat is een Azure-resourcegroep?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) voor informatie over het gebruik van resourcegroepen om uw gegevens en services in dezelfde regio te groeperen om de uitstaande kosten te beperken. 

Op de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) vindt u gedetailleerde prijsinformatie op basis van accounttype, opslagcapaciteit, replicatie en transacties. In [Prijsinformatie over gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) vindt u gedetailleerde informatie over de prijzen voor uitgaande gegevens. Met de [Prijscalculator van Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) kunt u een schatting maken van uw kosten.