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
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517348"
---
# <a name="explore-the-synapse-knowledge-center"></a>Het Synapse Knowledge Center verkennen

In deze zelfstudie leert u hoe u het Synapse Studio Knowledge Center gebruikt.

## <a name="finding-to-the-knowledge-center"></a>Zoeken naar de Knowledge Center

Er zijn twee manieren om het Knowledge Center te vinden in Synapse Studio:

  1. Klik in de Home-hub rechtsboven op de pagina op **Learn**.
  2. Klik in de menubalk bovenaan op **?** en vervolgens **Knowledge Center**.

Kies een van beide methoden en open het **Knowledge Center**.

## <a name="exploring-the-knowledge-center"></a>De Knowledge Center

Zodra deze zichtbaar is, ziet u dat de Knowledge Center **u** drie dingen kunt doen:
* **Voorbeelden direct gebruiken**. Als u een snel voorbeeld wilt van hoe Synapse werkt, kiest u deze optie.
* **Door galerie bladeren**. Met deze optie kunt u voorbeeldgegevenssets koppelen en voorbeeldcode toevoegen in de vorm van SQL-scripts, -notebooks en -pijplijnen.
* **Rondleiding Synapse Studio**. Met deze optie krijgt u een korte rondleiding door de basisonderdelen van Synapse Studio. Dit is handig als u Synapse Studio nog nooit eerder hebt gebruikt.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Voorbeelden onmiddellijk gebruiken: Drie voorbeelden om snel aan de slag te gaan

Deze sectie bestaat uit drie items:
* Voorbeeldgegevens verkennen met Spark
* Query's uitvoeren op gegevens met SQL
* Een externe tabel maken met SQL

1. Klik in **Knowledge Center** op **Voorbeelden onmiddellijk gebruiken.**
1. Selecteer **Query uitvoeren op gegevens met SQL**.
1. Klik **op Voorbeeld gebruiken.**
1. Er wordt een nieuw SQL-voorbeeldscript geopend.
1. Schuif naar de eerste query (regels 28 tot en met 32) en selecteer de querytekst.
1. Klik op Run. Er wordt alleen code uitgevoerd die u hebt geselecteerd.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Galerie: Een verzameling voorbeeldgegevenssets en voorbeeldcode

1. Ga naar het **Knowledge Center** en klik op Bladeren **in galerie.**
1. Selecteer het **tabblad SQL-scripts** bovenaan.
1. Selecteer **Load the New York Taxicab dataset** Data ingestion sample en klik op **Continue.**
1. Kies **onder SQL-pool** **een bestaande pool selecteren** en selecteer **SQLPOOL1** en selecteer de **SQLPOOL1-database** die u eerder hebt gemaakt.
1. Klik **op Script openen.**
1. Er wordt een nieuw SQL-voorbeeldscript geopend.
1. Klik op **Uitvoeren**
1. Hiermee maakt u verschillende tabellen voor alle NYC Taxi-gegevens en laadt u deze met behulp van de T-SQL COPY-opdracht. Als u deze tabellen in de vorige stappen voor snel starten hebt gemaakt, selecteert en voert u alleen code uit voor CREATE en COPY voor tabellen die niet bestaan.

    > [!NOTE] 
    > Wanneer u de voorbeeldgalerie gebruikt voor een SQL-script met een toegewezen SQL-pool (voorheen SQL DW), kunt u alleen een bestaande toegewezen SQL-pool (voorheen SQL DW) gebruiken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beheerder toevoegen](get-started-add-admin.md)

