---
title: Platte bestanden coderen of decoderen
description: Versleutelen of decoderen van platte bestanden voor bedrijfs integratie in Azure Logic Apps met behulp van de Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001482"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Platte bestanden coderen en decoderen in Azure Logic Apps met behulp van de Enterprise Integration Pack

Voordat u XML-inhoud naar een zakelijke partner verzendt in een Business-to-business-scenario (B2B), wilt u die inhoud mogelijk eerst coderen. Door een logische app te maken, kunt u platte bestanden coderen en decoderen met behulp van de connector voor **platte** bestanden. Uw logische app kan deze XML-inhoud ophalen uit verschillende bronnen, zoals de trigger voor aanvragen, een andere app of andere [connectors die door Azure Logic apps worden ondersteund](../connectors/apis-list.md). Zie [Wat is Azure Logic apps](logic-apps-overview.md)? voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waarvoor u de **platte bestands** connector wilt gebruiken en een trigger waarmee de werk stroom van de logische app wordt gestart. De connector voor **platte bestanden** biedt alleen acties, niet voor triggers. U kunt de trigger of een andere actie gebruiken voor het invoeren van de XML-inhoud in uw logische app voor het coderen of decoderen. Als u geen ervaring hebt met Logic apps, raadpleegt u [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en dat is [gekoppeld aan de logische app](./logic-apps-enterprise-integration-create-integration-account.md#link-account) , waar u van plan bent om de **platte bestands** connector te gebruiken. De logische app en het integratie account moeten zich op dezelfde locatie of Azure-regio bevinden.

* Een plat bestands [schema](logic-apps-enterprise-integration-schemas.md) dat u hebt geüpload naar uw integratie account voor het coderen of decoderen van de XML-inhoud

* Ten minste twee [handels partners](logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratie account

## <a name="add-flat-file-encode-action"></a>Actie voor het coderen van plat bestand toevoegen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap**  >  **een actie toevoegen** onder de trigger of actie in uw logische app. In dit voor beeld wordt de trigger voor aanvragen gebruikt, die wordt genoemd **Wanneer een HTTP-aanvraag wordt ontvangen**, en worden binnenkomende aanvragen van buiten de logische app afgehandeld.

   > [!TIP]
   > Het opgeven van een JSON-schema is optioneel. Als u een voor beeld van een nettolading van de inkomende aanvraag hebt, selecteert u **voor beeld Payload gebruiken om een schema te genereren**, voert u de voor beeld-nettolading in en selecteert u **gereed**. Het schema wordt weer gegeven in het vak **JSON-schema van aanvraag tekst** .

1. Onder **Kies een actie**, voert u in `flat file` . Selecteer in de lijst acties deze actie: **platte bestands codering**

   ![Selecteer actie voor plat bestand coderen](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Klik in het vak **inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst, in de sectie **Wanneer een HTTP-aanvraag wordt ontvangen** , de eigenschap **Body** , die de uitvoer van de aanvraag tekst bevat van de trigger en de inhoud die moet worden gecodeerd.

   ![Inhoud selecteren die moet worden gecodeerd vanuit de lijst met dynamische inhoud](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Als de eigenschap **Body** niet wordt weer geven in de lijst met dynamische inhoud, selecteert u **meer weer geven** naast het gedeelte label **Wanneer een HTTP-aanvraag is ontvangen** .
   > U kunt de inhoud die u wilt decoderen ook rechtstreeks invoeren in het vak **inhoud** .

1. Selecteer in de lijst **schema naam** het schema dat zich in het gekoppelde integratie account bevindt dat voor code ring moet worden gebruikt, bijvoorbeeld:

   ![Selecteer het schema dat voor de code ring moet worden gebruikt](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Als er geen schema in de lijst wordt weer gegeven, bevat uw integratie account geen schema bestanden voor code ring. Upload het schema dat u wilt gebruiken voor uw integratie account.

1. Sla uw logische app op. Als u uw connector wilt testen, maakt u een aanvraag voor het HTTPS-eind punt, dat wordt weer gegeven in de **http post-URL-** eigenschap van de trigger van de aanvraag, en neemt u de XML-inhoud op die u wilt coderen in de hoofd tekst van de aanvraag.

U bent nu klaar met het instellen van de actie voor het coderen van platte bestanden. In een echte wereld-app wilt u mogelijk de gecodeerde gegevens opslaan in een LOB-app (line-of-Business), zoals Sales Force. Of u kunt de gecodeerde gegevens verzenden naar een handels partner. Als u de uitvoer van de coderings actie naar Sales Force of naar uw handels partner wilt verzenden, gebruikt u de andere [Connect oren die beschikbaar zijn in azure Logic apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Actie voor het decoderen van plat bestand toevoegen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap**  >  **een actie toevoegen** onder de trigger of actie in uw logische app. In dit voor beeld wordt de trigger voor aanvragen gebruikt, die wordt genoemd **Wanneer een HTTP-aanvraag wordt ontvangen**, en worden binnenkomende aanvragen van buiten de logische app afgehandeld.

   > [!TIP]
   > Het opgeven van een JSON-schema is optioneel. Als u een voor beeld van een nettolading van de inkomende aanvraag hebt, selecteert u **voor beeld Payload gebruiken om een schema te genereren**, voert u de voor beeld-nettolading in en selecteert u **gereed**. Het schema wordt weer gegeven in het vak **JSON-schema van aanvraag tekst** .

1. Onder **Kies een actie**, voert u in `flat file` . Selecteer in de lijst acties deze actie: **plat bestand decoderen**

   ![De actie voor het decoderen van een plat bestand selecteren](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Klik in het vak **inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst, in de sectie **Wanneer een HTTP-aanvraag wordt ontvangen** , de eigenschap **Body** , die de uitvoer van de aanvraag tekst bevat van de trigger en de inhoud die moet worden gedecodeerd.

   ![Inhoud selecteren om uit de lijst met dynamische inhoud te decoderen](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Als de eigenschap **Body** niet wordt weer geven in de lijst met dynamische inhoud, selecteert u **meer weer geven** naast het gedeelte label **Wanneer een HTTP-aanvraag is ontvangen** . U kunt de inhoud die u wilt decoderen ook rechtstreeks invoeren in het vak **inhoud** .

1. Selecteer in de lijst **schema naam** het schema dat zich in het gekoppelde integratie account bevindt dat moet worden gebruikt voor het decoderen, bijvoorbeeld:

   ![Schema selecteren dat moet worden gebruikt voor het decoderen](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Als er geen schema in de lijst wordt weer gegeven, bevat uw integratie account geen schema bestanden voor het decoderen. Upload het schema dat u wilt gebruiken voor uw integratie account.

1. Sla uw logische app op. Als u uw connector wilt testen, maakt u een aanvraag voor het HTTPS-eind punt, dat wordt weer gegeven in de **http post-URL-** eigenschap van de aanvraag trigger, en neemt u de XML-inhoud op die u wilt decoderen in de hoofd tekst van de aanvraag.

U bent nu klaar met het instellen van de actie voor het decoderen van platte bestanden. In een echte wereld-app wilt u mogelijk de gedecodeerde gegevens opslaan in een LOB-app (line-of-Business), zoals Sales Force. U kunt ook de gedecodeerde gegevens verzenden naar een handels partner. Als u de uitvoer wilt verzenden van de decodeer actie naar Sales Force of naar uw handels partner, gebruikt u de andere [connectors die beschikbaar zijn in azure Logic apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
