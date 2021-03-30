---
title: Quota verhoging aanvragen voor Azure Cosmos DB resources
description: Meer informatie over het aanvragen van een quota verhoging voor Azure Cosmos DB-resources. U leert ook hoe u een abonnement inschakelt voor toegang tot een regio.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090032"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Quota verhoging aanvragen voor Azure Cosmos DB resources
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De resources in Azure Cosmos DB hebben [standaard quota's/limieten](concepts-limits.md). Het is echter mogelijk dat er een geval is waarin uw werk belasting meer quota nodig heeft dan de standaard waarde. In dat geval moet u contact met het Azure Cosmos DB-team bereiken om een quotum toename aan te vragen. In dit artikel wordt uitgelegd hoe u een quotum verhoging voor Azure Cosmos DB resources kunt aanvragen. U leert ook hoe u een abonnement inschakelt voor toegang tot een regio.

## <a name="create-a-new-support-request"></a>Een nieuwe ondersteunings aanvraag maken

Als u een quotum toename wilt aanvragen, moet u een nieuwe ondersteunings aanvraag maken met de details van uw workload. Het Azure Cosmos DB-team evalueert vervolgens uw aanvraag en keurt deze goed. Gebruik de volgende stappen om een nieuwe ondersteunings aanvraag te maken op basis van de Azure Portal:

1. Meld u aan bij Azure Portal.

1. Selecteer in het menu aan de linkerkant **Help en ondersteuning** en selecteer vervolgens **nieuwe ondersteunings aanvraag**.

1. Vul op het tabblad **basis beginselen** de volgende gegevens in:

   * Selecteer bij **Probleemtype** **Service- en abonnementslimieten (quota)**
   * Selecteer bij **abonnement** het abonnement waarvoor u het quotum wilt verhogen.
   * Selecteer **Cosmos DB** bij **quotum type**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Een nieuwe Cosmos DB ondersteunings aanvraag voor een quotum verhoging maken":::

1. Voer op het tabblad **Details** de gegevens in die overeenkomen met uw quotum aanvraag. De informatie op dit tabblad wordt gebruikt om het probleem verder te beoordelen en de ondersteunings technicus te helpen bij het oplossen van het probleem.

1. Vul de volgende gegevens in dit formulier in:

   * **Beschrijving**: Geef een korte beschrijving van uw aanvraag, zoals uw workload, waarom de standaard waarden niet voldoende zijn. Op basis van het resource type waarvoor u het quotum wilt verg Roten, is het verplicht om de volgende gegevens op te geven in het veld **Beschrijving** :

     **Regio's aanvragen** Als uw aanvraag overeenkomt met het toevoegen van een regio aan de lijst met toegestane gegevens, moet u de volgende waarden opgeven:

        * Regio naam
        * Abonnements-id

     **Aanvragen voor doorvoer limiet** Als uw aanvraag overeenkomt met het verhogen van het quotum voor de door Voer, moet u ervoor zorgen dat u de volgende waarden opgeeft:

        * Databasenaam
        * Abonnements-id
        * Nieuwe doorvoer limiet

     **Limiet aanvragen voor database accounts** Als uw aanvraag overeenkomt met het verhogen van het quotum voor het aantal database accounts in een abonnement, zorg er dan voor dat u de volgende waarden opgeeft:

       * Abonnements-id
       * Limiet voor nieuwe database accounts

   * **Bestand uploaden**: Upload de diagnostische bestanden of andere bestanden die u relevant vindt voor het ondersteunings verzoek. Zie het artikel over [Azure-ondersteuning]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) voor meer informatie over de richt lijnen voor het uploaden van bestanden.

   * **Ernst**: Kies een van de beschik bare Ernst niveaus op basis van de bedrijfs impact.

   * **Voorkeurs methode voor contact**: u kunt ervoor kiezen om contact op te nemen via **e-mail** of **telefonisch**.

1. Vul de resterende gegevens in, zoals de beschik baarheid, de taal van de ondersteuning, de contact gegevens, het e-mail adres en het telefoon nummer van het formulier.

1. Selecteer **volgende: controleren + maken**. Valideer de verstrekte informatie en selecteer **maken** om een ondersteunings aanvraag te maken.

Binnen 24 uur evalueert het Azure Cosmos DB-ondersteunings team uw aanvraag en gaat u terug naar u.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure Cosmos DB standaard service quota's](concepts-limits.md)
