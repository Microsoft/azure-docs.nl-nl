---
title: 'Zelfstudie: Aan de slag met het Synapse Knowledge Center'
description: In deze zelfstudie leert u hoe u het Synapse Knowledge Center gebruikt.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 0314f5e55a476cd1cd1f8a31bd918e0ebb64c75f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254235"
---
# <a name="explore-the-synapse-knowledge-center"></a>Het Synapse Knowledge Center verkennen

In deze zelfstudie leert u hoe u het Synapse Studio Knowledge Center gebruikt.

## <a name="getting-to-the-knowledge-center"></a>Het Knowledge Center verkennen

Er zijn twee manieren om het Knowledge Center te vinden in Synapse Studio:

  1. Klik in de Home-hub rechtsboven op de pagina op **Learn**.
  2. Klik in de menubalk bovenaan op **?** en vervolgens op **Knowledge Center**.

Kies een van beide methoden en open het **Knowledge Center**.

## <a name="overview"></a>Overzicht

In het **Knowledge Center** kunt u drie dingen doen:
* **Voorbeelden direct gebruiken**. Als u een snel voorbeeld wilt van hoe Synapse werkt, kiest u deze optie.
* **Door galerie bladeren**. Met deze optie kunt u voorbeeldgegevenssets koppelen en voorbeeldcode toevoegen in de vorm van SQL-scripts, -notebooks en -pijplijnen.
* **Rondleiding door Synapse Studio**. Met deze optie krijgt u een korte rondleiding door de basisonderdelen van Synapse Studio. Dit is handig als u Synapse Studio nog nooit eerder hebt gebruikt.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Blob-opslag verkennen met een serverloze SQL-pool

1. Ga naar het **Knowledge Center** en klik op **Voorbeelden direct gebruiken**
1. Selecteer **Gegevens opvragen met SQL** 
1. Klik op **Voorbeelden direct gebruiken**
1. Er wordt een nieuw SQL-script gemaakt.
1. Ga naar de eerste query (regels 28 tot en met 32) en selecteer de querytekst
1. Klik op Run. De tekst die u hebt geselecteerd, wordt uitgevoerd.

## <a name="loading-more-nyc-taxi-data"></a>Meer NYC Taxi-gegevens laden
1. Ga naar het **Knowledge Center-** en klik op **Door de galerie bladeren** 
1. Selecteer het tabblad **SQL-scripts** bovenaan
1. Selecteer **De dataset van New York Taxicab laden**
1. Kies onder **Invoer** de optie **Een bestaande pool selecteren** en selecteer **SQLDB1**
1. Klik op **Script openen**
1. Er wordt een nieuw SQL-script weergegeven.
1. Klik op **Uitvoeren**
1. Hiermee maakt u verschillende tabellen voor alle NYC Taxi-gegevens en laadt u deze met behulp van de T-SQL COPY-opdracht.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
