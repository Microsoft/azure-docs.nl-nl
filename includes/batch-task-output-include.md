---
title: bestand opnemen
description: bestand opnemen
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028221"
---
Een taak die wordt uitgevoerd in Azure Batch kan uitvoer gegevens produceren wanneer deze wordt uitgevoerd. Taak uitvoer gegevens moeten vaak worden opgeslagen om te worden opgehaald door andere taken in de taak, de client toepassing die de taak heeft uitgevoerd, of beide. Taken schrijven uitvoer gegevens naar het bestands systeem van een batch Compute-knoop punt, maar alle gegevens op het knoop punt gaan verloren wanneer de installatie kopie wordt gewijzigd of wanneer het knoop punt de groep verlaat. Taken kunnen ook een Bewaar periode voor bestanden hebben, waarna de bestanden die door de taak zijn gemaakt, worden verwijderd. Daarom is het belang rijk dat u de taak uitvoer persistent maakt die u later nodig hebt voor een gegevens archief, zoals [Azure Storage](../articles/storage/index.yml).

Zie [batch-accounts en Azure Storage accounts](../articles/batch/accounts.md#azure-storage-accounts)voor opties voor opslag accounts in batch.