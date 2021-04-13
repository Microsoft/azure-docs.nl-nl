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
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307891"
---
# <a name="explore-the-synapse-knowledge-center"></a>Het Synapse Knowledge Center verkennen

In deze zelfstudie leert u hoe u het Synapse Studio Knowledge Center gebruikt.

## <a name="introduction-to-the-knowledge-center"></a>Inleiding tot het kennis centrum

Er zijn twee manieren om het Knowledge Center te vinden in Synapse Studio:

  1. Klik in de Home-hub rechtsboven op de pagina op **Learn**.
  2. Klik in de menubalk bovenaan op **?** en vervolgens het **kennis centrum**.

Kies een van beide methoden en open het **Knowledge Center**.

Zodra deze weer gegeven is, ziet u dat u met het **kennis centrum** drie dingen kunt doen:
* **Voorbeelden direct gebruiken**. Als u een snel voorbeeld wilt van hoe Synapse werkt, kiest u deze optie.
* **Door galerie bladeren**. Met deze optie kunt u voorbeeldgegevenssets koppelen en voorbeeldcode toevoegen in de vorm van SQL-scripts, -notebooks en -pijplijnen.
* **Tour Synapse Studio**. Met deze optie krijgt u een korte rondleiding door de basisonderdelen van Synapse Studio. Dit is handig als u Synapse Studio nog nooit eerder hebt gebruikt.

## <a name="exploring-use-samples-immediately"></a>Verkennen: voor beelden direct gebruiken

Deze sectie bevat drie items:
* Voorbeeld gegevens verkennen met Spark
* Query's uitvoeren op gegevens met SQL
* Externe tabel maken met SQL

1. Klik in het **kennis centrum** onmiddellijk op voor **beelden gebruiken**.
1. Selecteer **query gegevens met SQL**.
1. Klik op voor **beeld gebruiken**.
1. Er wordt een nieuw voor beeld-SQL-script geopend.
1. Ga naar de eerste query (regels 28 tot en met 32) en selecteer de query tekst.
1. Klik op Run. Hiermee wordt alleen de code uitgevoerd die u hebt geselecteerd.

## <a name="loading-more-nyc-taxi-data"></a>Meer NYC Taxi-gegevens laden

1. Ga naar het **kennis centrum** en klik op **Bladeren in Galerie**.
1. Selecteer bovenaan het tabblad **SQL-scripts** .
1. Selecteer het voor beeld van de gegevens opname van **het New York over taxi's-gegevensset laden** en klik op **door gaan**.
1. Kies **onder SQL-groep** de optie **Selecteer een bestaande groep** en selecteer **SQLPOOL1**, en selecteer de **SQLPOOL1** -data base die u eerder hebt gemaakt.
1. Klik op **script openen**.
1. Er wordt een nieuw voor beeld-SQL-script geopend.
1. Klik op **Uitvoeren**
1. Hiermee maakt u verschillende tabellen voor alle NYC Taxi-gegevens en laadt u deze met behulp van de T-SQL COPY-opdracht. Als u deze tabellen hebt gemaakt in de vorige Snelstartgids voor snel starten, selecteert en voert u alleen code uit om tabellen te maken en te kopiëren die niet bestaan.

    > [!NOTE] 
    > Wanneer u de voorbeeldgalerie gebruikt voor een SQL-script met een toegewezen SQL-pool (voorheen SQL DW), kunt u alleen een bestaande toegewezen SQL-pool (voorheen SQL DW) gebruiken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beheerder toevoegen](get-started-add-admin.md)

