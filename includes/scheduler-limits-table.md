---
title: bestand opnemen
description: bestand opnemen
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75392424"
---
In de volgende tabel worden de belangrijkste quota's, limieten, standaard waarden en beperkingen in azure scheduler beschreven.

| Resource | Limiet beschrijving |
| -------- | ----------------- |
| **Taak grootte** | De maximale taak grootte is 16.000. Als een PUT-of een PATCH-bewerking resulteert in een grootte van een taak die groter is dan deze limiet, wordt de status code van een ongeldige aanvraag van 400 geretourneerd. | 
| **Taak verzamelingen** | Het maximum aantal taak verzamelingen per Azure-abonnement is 200.000. | 
| **Taken per verzameling** | Het maximum aantal taken is standaard vijf taken in een gratis taak verzameling en 50-taken in een standaard taak verzameling. U kunt het maximum aantal taken voor een taak verzameling wijzigen. Alle taken in een taak verzameling zijn beperkt tot de waarde die is ingesteld voor de taak verzameling. Als u probeert meer taken te maken dan het quotum voor het maximum aantal taken, mislukt de aanvraag met een status code van 409-conflicten. | 
| **Tijd tot begin tijd** | De maximum tijd voor het starten van is 18 maanden. |
| **Herhalings bereik** | De maximale terugkeer patroon is 18 maanden. | 
| **Frequentie** | Het maximum frequentie quotum is standaard één uur in een gratis taak verzameling en één minuut in een standaard taak verzameling. <p>U kunt de maximum frequentie voor een taak verzameling lager maken dan het maximum. Alle taken in de taak verzameling zijn beperkt tot de waarde die is ingesteld voor de taak verzameling. Als u probeert een taak te maken met een hogere frequentie dan de maximum frequentie voor de taak verzameling, mislukt de aanvraag met een status code van 409-conflicten. | 
| **Hoofdtekst grootte** | De maximale hoofdtekst grootte voor een aanvraag is 8.192 tekens. |
| **Grootte van aanvraag-URL** | De maximale grootte voor een aanvraag-URL is 2.048 tekens. |
| **Aantal headers** | Het maximum aantal headers is 50 kopteksten. | 
| **Cumulatieve header grootte** | De maximale cumulatieve header grootte is 4.096 tekens. |
| **Out** | De time-out van de aanvraag is statisch, dat wil zeggen, niet configureerbaar. en is 60 seconden voor HTTP-acties. Volg de asynchrone HTTP-protocollen voor meer bewerkingen. U kunt bijvoorbeeld een 202 direct retour neren, maar op de achtergrond blijven werken. | 
| **Jobgeschiedenis** | De maximale tekst van het antwoord dat is opgeslagen in de taak geschiedenis is 2.048 bytes. |
| **Taak geschiedenis retentie** | De taak geschiedenis wordt Maxi maal twee maanden of tot de laatste 1.000 uitvoeringen bewaard. | 
| **Voltooide en mislukte taak behoud** | Voltooide en mislukte taken worden 60 dagen bewaard. |
||| 

