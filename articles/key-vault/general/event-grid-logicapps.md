---
title: E-mail wanneer de status van het geheim van de Key Vault is gewijzigd
description: Handleiding voor het gebruik van Logic Apps om te reageren op wijzigingen in Key Vault-geheimen
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e6ec06e3df87e0ad0e50efca24439435d655f6a1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752098"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Logic Apps gebruiken om een e-mail te ontvangen over statuswijzigingen van sleutelkluisgeheimen

In deze handleiding leert u hoe u kunt reageren op Azure Key Vault-gebeurtenissen die via [Azure Event Grid](../../event-grid/index.yml) worden ontvangen met [Azure Logic Apps](../../logic-apps/index.yml). Aan het einde van het artikel beschikt u over een logische Azure-app die een e-mailmelding verstuurt wanneer er een geheim wordt gemaakt in Azure Key Vault.

Zie [Key Vault bewaken met Azure Event Grid](event-grid-overview.md) voor een overzicht van Azure Key Vault/Azure Event Grid-integratie.

## <a name="prerequisites"></a>Vereisten

- Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund (bijvoorbeeld Office 365 Outlook). Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](/connectors) voor een volledige lijst met ondersteunde Logic App-connectors.
- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een sleutelkluis in uw Azure-abonnement. U kunt snel een nieuwe sleutelkluis maken aan de hand van de stappen in [Een geheim instellen en ophalen van Azure Key Vault met Azure CLI](../secrets/quick-create-cli.md)
- Geregistreerde Event Grid als resourceprovider. Zie de [Registraties van resourceproviders](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Een logische app maken via Event Grid

Maak eerst een logische app met Event grid-handler en abonneer u op Azure Key Vault-gebeurtenissen 'SecretNewVersionCreated'.

Volg de volgende stappen als u een Azure Event Grid-abonnement wilt maken:

1. Ga in Azure Portal naar uw sleutelkluis, selecteer **Gebeurtenissen > Aan de slag** en klik op **Logic Apps**

    
    ![Key Vault - gebeurtenissenpagina](../media/eventgrid-logicapps-kvsubs.png)

1. Valideer in **Logic Apps Designer** de verbinding en klik op **Doorgaan** 
 
    ![Logic App Designer - verbinding](../media/eventgrid-logicappdesigner1.png)

1. Ga als volgt te werk op het scherm **Wanneer een resourcegebeurtenis zich voordoet**:
    - Laat **Abonnement** en **Resourcenaam** op de standaardwaarde staan.
    - Selecteer **Microsoft.KeyVault.vaults** voor het **Resourcetype**.
    - Selecteer **Microsoft.KeyVault.SecretNewVersionCreated** voor **Gebeurtenistype-item - 1**.

    ![Logic App Designer - gebeurtenishandler](../media/eventgrid-logicappdesigner2.png)

1. Selecteer **+ Nieuwe stap**. Hiermee wordt een nieuw venster geopend om een actie te kiezen.
1. Zoek naar **e-mail**. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook** gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar.
1. Selecteer de actie **Een e-mail verzenden (V2)** .

   ![Logic App Designer - e-mail verzenden](../media/eventgrid-logicappdesigner3.png)

1. Stel de e-mailsjabloon samen:
    - **Aan:** Voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Voor deze zelfstudie gebruikt u een e-mailaccount dat u kunt gebruiken voor het testen.
    - **Onderwerp** en **Hoofdtekst**: typ hier het onderwerp en de tekst voor uw e-mail. Selecteer de JSON-eigenschappen van het selector-hulpprogramma om dynamische inhoud op basis van gebeurtenisgegevens toe te voegen. U kunt de gegevens van de gebeurtenis ophalen met `@{triggerBody()?['Data']}`.

    Uw e-mailsjabloon ziet er nu misschien uit als in dit voorbeeld.

    ![Logic App Designer - e-mailtekst](../media/eventgrid-logicappdesigner4.png)

8. Klik op **Opslaan als**.
9. Voer een **Naam** in voor een nieuwe logische app en klik op **Maken**.
    
    ![Logic App Designer - maken](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testen en verifiëren

1.  Ga naar de sleutelkluis op Azure Portal en selecteer **Gebeurtenissen > Gebeurtenisabonnementen**.  Controleren of een nieuw abonnement is gemaakt
    
    ![Logic App Designer - testen en verifiëren](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Ga naar de sleutelkluis, selecteer **Geheimen** en selecteer **+ Genereren/importeren**. Maak een nieuw geheim voor testdoeleinden. Geef de sleutel een naam en behoud voor de resterende parameters de standaardinstellingen.

    ![Key Vault - geheim maken](../media/eventgrid-logicapps-kv-create-secret.png)

1. Geef op het scherm **Een geheim maken** een naam en een willekeurige waarde op en selecteer **Maken**.

Wanneer het geheim wordt gemaakt, wordt er een e-mailbericht ontvangen op de geconfigureerde adressen.

## <a name="next-steps"></a>Volgende stappen

- Overzicht: [Key Vault bewaken met Azure Event Grid](event-grid-overview.md)
- Procedure: [Key Vault-meldingen routeren naar Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Meer informatie over [Azure Event Grid](../../event-grid/index.yml).
- Meer informatie over de [Logic Apps-functie van Azure App Service](../../logic-apps/index.yml).