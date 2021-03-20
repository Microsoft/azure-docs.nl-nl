---
title: Waarschuwingen maken voor Azure Cosmos DB met behulp van Azure Monitor
description: Meer informatie over het instellen van waarschuwingen voor Azure Cosmos DB met behulp van Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339535"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Waarschuwingen maken voor Azure Cosmos DB met behulp van Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Waarschuwingen worden gebruikt om terugkerende tests in te stellen voor het bewaken van de beschik baarheid en reactie tijd van uw Azure Cosmos DB-resources. Waarschuwingen kunnen u een melding sturen in de vorm van een e-mail bericht of een Azure-functie uitvoeren als een van uw metrische gegevens de drempel waarde bereikt of als er een specifieke gebeurtenis in het activiteiten logboek wordt vastgelegd.

U kunt een waarschuwing ontvangen op basis van de metrische gegevens of de gebeurtenissen in het activiteiten logboek voor uw Azure Cosmos-account:

* **Metrische gegevens** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. Als bijvoorbeeld het totale aantal verbruikte aanvraag eenheden groter is dan 1000 RU/s. Deze waarschuwing wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan. Zie het artikel over [bewakings gegevens](monitor-cosmos-db-reference.md#metrics) voor een overzicht van de beschik bare metrieken in azure Cosmos db.

* **Activiteiten logboek gebeurtenissen** : deze waarschuwing wordt geactiveerd wanneer een bepaalde gebeurtenis zich voordoet. Bijvoorbeeld wanneer de sleutels van uw Azure Cosmos-account worden geopend of vernieuwd.

U kunt waarschuwingen instellen in het deel venster Azure Cosmos DB of de Azure Monitor-service in de Azure Portal. Beide interfaces bieden dezelfde opties. In dit artikel wordt beschreven hoe u waarschuwingen instelt voor Azure Cosmos DB met behulp van Azure Monitor.

## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken

In deze sectie wordt beschreven hoe u een waarschuwing maakt wanneer u een HTTP-status code 429 ontvangt, die wordt ontvangen wanneer het aantal aanvragen is beperkt. Voor beelden is het mogelijk dat u een waarschuwing ontvangt wanneer er 100 of meer tarieven voor beperkte aanvragen zijn. In dit artikel wordt beschreven hoe u een waarschuwing voor een dergelijk scenario configureert met behulp van de HTTP-status code. U kunt de vergelijk bare stappen gebruiken om ook andere typen waarschuwingen te configureren. u hoeft alleen maar een andere voor waarde te kiezen op basis van uw vereisten.

1. Meld u aan bij de [Azure Portal.](https://portal.azure.com/)

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **waarschuwingen**.

1. Selecteer de knop nieuwe waarschuwings regel om het deel venster waarschuwings regel maken te openen.  

1. Vul de **Scope** sectie in:

   * Open het deel venster **resource selecteren** en configureer het volgende:

   * Kies de naam van uw **abonnement** .

   * Selecteer **Azure Cosmos DB accounts** voor het **resource type**.

   * De **locatie** van uw Azure Cosmos-account.

   * Nadat u de details hebt ingevuld, wordt een lijst met Azure Cosmos-accounts in het geselecteerde bereik weer gegeven. Kies het abonnement waarvoor u waarschuwingen wilt configureren en selecteer **gereed**.

1. Vul de sectie **voor waarde** in:

   * Open het deel venster **voor waarde selecteren** om de pagina **signaal logica configureren** te openen en configureer het volgende:

   * Selecteer een signaal. Het **signaal type** kan een **metrische waarde** of een **activiteiten logboek** zijn. Kies **metrische gegevens** voor dit scenario. Omdat u een waarschuwing wilt ontvangen wanneer er sprake is van problemen met de frequentie limiet voor de totale metrische aanvraag eenheden.

   * **Alles** selecteren voor de **Monitor service**

   * Kies een **signaal naam**. Als u een waarschuwing wilt ontvangen voor HTTP-status codes, kiest u het totale signaal voor **aanvraag eenheden** .

   * Op het volgende tabblad kunt u de logica voor het activeren van een waarschuwing definiëren en de grafiek gebruiken om trends van uw Azure Cosmos-account weer te geven. De metrische gegevens voor de **totale aanvraag eenheden** ondersteunen dimensies. Met deze dimensies kunt u filteren op de metriek. Als u geen dimensie selecteert, wordt deze waarde genegeerd.

   * Kies **status** code als **dimensie naam**. Selecteer **aangepaste waarde toevoegen** en stel de status code in op 429.

   * Stel in de logica van de **waarschuwing** de **drempel waarde** in op **statisch**. De statische drempel waarde gebruikt een door de gebruiker gedefinieerde drempelwaarde voor het evalueren van de regel, terwijl de dynamische drempel waarden gebruikmaken van ingebouwde machine learning algoritmen om voortdurend het meet patroon voor metrische gegevens te ontdekken en de drempel waarden automatisch te berekenen.

   * Stel de **operator** in **op groter dan**, **het aggregatie type** in **totaal** en de **drempel waarde** op **100**. Als uw client meer dan 100 aanvragen met een 429-status code heeft gezien, wordt de waarschuwing geactiveerd. U kunt ook het aggregatie type, de granulariteit van aggregatie en de frequentie van de evaluatie op basis van uw vereiste configureren.

   * Nadat u het formulier hebt ingevuld, selecteert u **gereed**. Op de volgende scherm afbeelding ziet u de details van de waarschuwings logica:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="De logica configureren voor het ontvangen van waarschuwingen voor een aantal beperkte/429-aanvragen":::

1. Vul de sectie **actie groep** in:

   * Selecteer een bestaande actie groep in het deel venster **regel maken** of maak een nieuwe actie groep. Met een actie groep kunt u de actie definiëren die moet worden uitgevoerd wanneer er een waarschuwings voorwaarde optreedt. Voor dit voor beeld maakt u een nieuwe actie groep om een e-mail melding te ontvangen wanneer de waarschuwing wordt geactiveerd. Open het deel venster **actie groep toevoegen** en vul de volgende gegevens in:

   * **Naam van de actie groep** -de naam van de actie groep moet uniek zijn binnen een resource groep.

   * **Korte naam** : de korte naam van de actie groep, deze waarde is opgenomen in e-mail-en SMS-meldingen om te identificeren welke actie groep de bron van de melding is.

   * Kies het abonnement en de resource groep waarin deze actie groep wordt gemaakt.  

   * Geef een naam op voor uw actie en selecteer **e-mail/SMS-bericht/push/Voice** als **actie type**. De volgende scherm afbeelding toont de details van het actie Type:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Het actie Type zoals e-mail melding configureren om de waarschuwing te ontvangen":::

1. Vul de sectie **Details van waarschuwings regel** in:

   * Definieer een naam voor de regel, geef een optionele beschrijving, het Ernst niveau van de waarschuwing, kies of u de regel wilt inschakelen bij het maken van de regel en selecteer vervolgens **regel waarschuwing maken** om de waarschuwing voor de metrische regel te maken.

Na het maken van de waarschuwing, wordt deze binnen 10 minuten actief.

## <a name="common-alerting-scenarios"></a>Algemene scenario's voor waarschuwingen

Hier volgen enkele scenario's waarin u waarschuwingen kunt gebruiken:

* Wanneer de sleutels van een Azure Cosmos-account worden bijgewerkt.
* Wanneer het gegevens-of index gebruik van een container, data base of regio groter is dan een bepaald aantal bytes.
* Wanneer het genormaliseerde RU/s-verbruik groter is dan een bepaald percentage. De genormaliseerde metrische gegevens over het gebruik van RU bieden het maximale doorvoer gebruik binnen een replicaset. Zie het artikel [genormaliseerde ru/s bewaken](monitor-normalized-request-units.md) voor meer informatie.  
* Wanneer een regio wordt toegevoegd, verwijderd of offline gaat.
* Wanneer een Data Base of container wordt gemaakt, verwijderd of bijgewerkt.
* Wanneer de door Voer van uw data base of de container wordt gewijzigd.

## <a name="next-steps"></a>Volgende stappen

* [Genormaliseerde ru/s-metriek bewaken](monitor-normalized-request-units.md) in azure Cosmos-container.
* De [door Voer of het gebruik van de aanvraag eenheid](monitor-request-unit-usage.md) van een bewerking in azure Cosmos DB bewaken.