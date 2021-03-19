---
title: AS2-berichten verzenden en ontvangen voor B2B
description: AS2-berichten uitwisselen voor B2B-scenario's voor bedrijfs integratie met behulp van Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91856667"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>AS2-berichten voor B2B Enterprise-integratie in Azure Logic Apps uitwisselen met Enterprise Integration Pack

> [!IMPORTANT]
> De oorspronkelijke AS2-connector wordt afgeschaft, dus tenzij u tracerings mogelijkheden nodig hebt, gebruikt u in plaats daarvan de **AS2 (v2)** -connector. Met uitzonde ring van het bijhouden van de v2-connector beschikt u over dezelfde mogelijkheden als voor de oorspronkelijke versie, is het systeem eigen voor de Logic Apps-runtime en heeft dat aanzienlijke prestatie verbeteringen in de bericht grootte, door Voer en latentie. Daarnaast hoeft de v2-connector geen verbinding te maken met uw integratie account. In plaats daarvan moet u, zoals beschreven in de vereisten, ervoor zorgen dat u uw integratie account koppelt aan de logische app waar u van plan bent om de connector te gebruiken.

Als u wilt werken met AS2-berichten in Azure Logic Apps, kunt u de AS2-connector gebruiken. Deze bevat triggers en acties die AS2-communicatie (versie 1,2) ondersteunen en beheren. Als u bijvoorbeeld beveiliging en betrouw baarheid tijdens het verzenden van berichten wilt instellen, kunt u deze acties gebruiken:

* [ **AS2 Codeer** actie](#encode) voor het leveren van versleuteling, digitale ondertekening en bevestigingen via bericht beschikkings meldingen (MDN), die ondersteuning bieden voor niet-afwijzing. Deze actie past bijvoorbeeld AS2/HTTP-headers toe en voert deze taken uit wanneer deze zijn geconfigureerd:

  * Ondertekent uitgaande berichten.
  * Versleutelt uitgaande berichten.
  * Hiermee comprimeert u het bericht.
  * Verzendt de bestands naam in de MIME-header.

* [ **AS2 decodeer** actie](#decode) voor het leveren van ontsleuteling, digitale ondertekening en bevestigingen via bericht beschikkings meldingen (MDN). Met deze actie worden bijvoorbeeld de volgende taken uitgevoerd:

  * Verwerkt AS2/HTTP-headers.
  * MDNs met de oorspronkelijke uitgaande berichten afgestemd.
  * Hiermee worden de records in de niet-bewaarde data base bijgewerkt en gecorreleerd.
  * Schrijft records voor AS2-status rapportage.
  * Voert inhoud van Payload uit als base64-gecodeerd.
  * Hiermee wordt bepaald of MDNs zijn vereist. Op basis van de AS2-overeenkomst bepaalt u of MDNs synchroon of asynchroon moet zijn.
  * Genereert synchrone of asynchrone MDNs op basis van de AS2-overeenkomst.
  * Hiermee stelt u de correlatie tokens en eigenschappen in op MDNs.

  Deze actie voert ook deze taken uit wanneer deze zijn geconfigureerd:

  * Hiermee wordt de hand tekening gecontroleerd.
  * Hiermee worden de berichten gedecodeerd.
  * Hiermee wordt het bericht gedecomprimeerd.
  * Dubbele bericht-id's controleren en weigeren.

In dit artikel wordt uitgelegd hoe u de AS2-code ring en decodeer acties kunt toevoegen aan een bestaande logische app.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app van waaruit u de AS2-connector wilt gebruiken en een trigger waarmee de werk stroom van de logische app wordt gestart. De AS2-connector biedt alleen acties, geen triggers. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en gekoppeld aan de logische app waar u de AS2-connector wilt gaan gebruiken. De logische app en het integratie account moeten zich op dezelfde locatie of Azure-regio bevinden.

* Ten minste twee [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratie account met behulp van de AS2-identiteits kwalificatie.

* Voordat u de AS2-connector kunt gebruiken, moet u een AS2- [overeenkomst](../logic-apps/logic-apps-enterprise-integration-agreements.md) maken tussen uw handels partners en de overeenkomst opslaan in uw integratie account.

* Als u [Azure Key Vault](../key-vault/general/overview.md) gebruikt voor certificaat beheer, controleert u of uw kluis sleutels de bewerkingen voor **versleuteling** en **ontsleuteling** toestaan. Anders mislukken de coderings-en decodeer acties.

  Ga in het Azure Portal naar de sleutel in uw sleutel kluis, Controleer de **toegestane bewerkingen** van uw sleutel en bevestig dat de bewerkingen voor **versleutelen** en **ontsleutelen** zijn geselecteerd, bijvoorbeeld:

  ![Controleer de bewerkingen van de kluis sleutel](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2-berichten coderen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure Portal](https://portal.azure.com)de logische app in de ontwerp functie voor logische apps.

1. Voeg in de ontwerp functie een nieuwe actie toe aan uw logische app.

1. Selecteer onder **Kies een actie** en het zoekvak de optie **Alle**. Voer in het zoekvak ' AS2 encode ' in en zorg ervoor dat u de actie AS2 (v2) selecteert: **AS2 Encode**

   ![Selecteer ' AS2 encode '](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Geef nu informatie op voor deze eigenschappen:

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Bericht dat moet worden gecodeerd** | De bericht lading |
   | **AS2 van** | De id van de afzender van het bericht zoals opgegeven door de AS2-overeenkomst |
   | **AS2 naar** | De id voor de ontvanger van het bericht zoals opgegeven door de AS2-overeenkomst |
   |||

   Bijvoorbeeld:

   ![Bericht coderings eigenschappen](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Als u problemen ondervindt bij het verzenden van ondertekende of versleutelde berichten, kunt u overwegen verschillende SHA256-algoritme indelingen te proberen. De AS2-specificatie biedt geen informatie over SHA256-indelingen, dus elke provider gebruikt een eigen implementatie of indeling.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2-berichten decoderen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure Portal](https://portal.azure.com)de logische app in de ontwerp functie voor logische apps.

1. Voeg in de ontwerp functie een nieuwe actie toe aan uw logische app.

1. Selecteer onder **Kies een actie** en het zoekvak de optie **Alle**. Voer in het zoekvak ' AS2 decode ' in en zorg ervoor dat u de actie AS2 (v2) selecteert: **AS2 decoderen**

   ![Selecteer ' AS2 decoderen '](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Selecteer voor het **bericht dat moet worden gecodeerd** en de eigenschappen van de **bericht koppen** deze waarden van vorige trigger of actie-uitvoer.

   Stel bijvoorbeeld dat uw logische app berichten ontvangt via een aanvraag trigger. U kunt de uitvoer van die Trigger selecteren.

   ![Hoofd tekst en kopteksten selecteren in uitvoer van aanvragen](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Voorbeeld

Zie de [sjabloon en het scenario voor de AS2 Logic-app](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)als u een volledig operationele logische app en een voor beeld van een AS2 scenario wilt implementeren.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/as2/). 

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), gebruikt de ISE-label versie van deze connector de [limieten voor B2B-berichten voor ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
