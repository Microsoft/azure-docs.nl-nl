---
title: Verbinding maken met Azure Blob Storage
description: Blobs maken en beheren in azure Storage-accounts met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677729"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Blobs in Azure Blob Storage maken en beheren met behulp van Azure Logic Apps

In dit artikel wordt uitgelegd hoe u bestanden die zijn opgeslagen als blobs in uw Azure Storage-account kunt openen en beheren in een logische app met de Azure Blob Storage-connector. Op die manier kunt u logische apps maken voor het automatiseren van taken en werk stromen voor het beheren van uw bestanden. U kunt bijvoorbeeld logische apps maken waarmee bestanden worden gemaakt, opgehaald, bijgewerkt en verwijderd in uw opslagaccount.

Stel dat u een hulp programma hebt dat wordt bijgewerkt op een Azure-website. die fungeert als de trigger voor uw logische app. Als deze gebeurtenis zich voordoet, kunt u uw logische app een bestand in de BLOB storage-container laten bijwerken. Dit is een actie in uw logische app.

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de naslag informatie voor [Azure Blob Storage-connector](/connectors/azureblobconnector/)voor connector-specifieke technische gegevens.

> [!IMPORTANT]
> Logic apps hebben geen directe toegang tot opslag accounts die zich achter firewalls bevinden, als ze zich in dezelfde regio. Als tijdelijke oplossing kunt u uw logische apps en opslag account in verschillende regio's hebben. Zie de sectie [toegang tot opslag accounts achter firewalls](#storage-firewalls) verderop in dit onderwerp voor meer informatie over het inschakelen van toegang vanaf Azure Logic apps voor opslag accounts achter firewalls.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limieten

* Met Azure Blob Storage-acties kunnen standaard bestanden worden gelezen of geschreven die *50 MB of kleiner* zijn. Voor het afhandelen van bestanden die groter zijn dan 50 MB maar Maxi maal 1024 MB, worden door Azure Blob Storage acties ondersteuning gegeven voor het delen van [berichten](../logic-apps/logic-apps-handle-large-messages.md). De actie **blob-inhoud ophalen** maakt impliciet gebruik van Chunking.

* Azure Blob Storage-Triggers bieden geen ondersteuning voor het delen van segmenten. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden van 50 MB of kleiner. Als u bestanden groter dan 50 MB wilt ophalen, volgt u dit patroon:

  * Gebruik een Azure Blob Storage-trigger die bestands eigenschappen retourneert, zoals **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)**.

  * Volg de trigger met de bewerking voor het **ophalen van blob-inhoud** in Azure Blob Storage, die het volledige bestand leest en impliciet gebruikmaakt van Chunking.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Azure-opslag account en een opslag container](../storage/blobs/storage-quickstart-blobs-portal.md)

* De logische app waar u toegang nodig hebt tot uw Azure Blob-opslag account. Als u uw logische app wilt starten met een Azure Blob Storage-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Trigger voor Blob-opslag toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

In dit voor beeld ziet u hoe u een werk stroom van een logische app kunt starten met de trigger **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)** wanneer de eigenschappen van een BLOB worden toegevoegd of bijgewerkt in uw opslag container.

1. Maak in de [Azure Portal](https://portal.azure.com) of Visual Studio een lege logische app, waarmee u de ontwerp functie voor logische apps kunt openen. In dit voor beeld wordt de Azure Portal gebruikt.

2. Voer in het zoekvak ' Azure Blob ' in als uw filter. Selecteer in de lijst triggers de gewenste trigger.

   In dit voor beeld wordt deze trigger gebruikt: **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)**

   ![Trigger voor Azure-Blob Storage selecteren](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Als u wordt gevraagd om de verbindings gegevens, [maakt u de verbinding voor Blob Storage nu](#create-connection). Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de trigger.

   Selecteer voor dit voor beeld de container en de map die u wilt bewaken.

   1. Selecteer in het vak **container** het mappictogram.

   2. Kies in de lijst met mappen de rechter hoek ( **>** ), en blader vervolgens tot u de gewenste map hebt gevonden en geselecteerd.

      ![Selecteer de opslagmap die met de trigger moet worden gebruikt](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecteer het interval en de frequentie voor hoe vaak de trigger de map moet controleren op wijzigingen.

4. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

5. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de trigger resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob-opslag actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. Voor dit voor beeld begint de logische app met de [trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md).

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps. In dit voor beeld wordt de Azure Portal gebruikt.

2. Kies **nieuwe stap** onder de trigger of actie in de ontwerp functie voor logische apps.

   ![Nieuwe stap toevoegen aan de werk stroom van een logische app](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer **een actie toevoegen**.

3. Voer in het zoekvak ' Azure Blob ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   In dit voor beeld wordt de volgende actie gebruikt: **blob-inhoud ophalen**

   ![Azure-Blob Storage actie selecteren](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Als u wordt gevraagd om de verbindings gegevens, [maakt u nu uw Azure Blob Storage-verbinding](#create-connection).
Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de actie.

   Voor dit voor beeld selecteert u het gewenste bestand.

   1. Selecteer het mappictogram in het vak **BLOB** .
  
      ![Selecteer de opslagmap die u met de actie wilt gebruiken](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Zoek en selecteer het gewenste bestand op basis van het **id-** nummer van de blob. U vindt dit **id-** nummer in de meta gegevens van de blob die worden geretourneerd door de eerder beschreven Blob Storage-trigger.

5. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.
Als u uw logische app wilt testen, moet u ervoor zorgen dat de geselecteerde map een BLOB bevat.

In dit voor beeld wordt alleen de inhoud van een BLOB opgehaald. Als u de inhoud wilt weer geven, voegt u een andere actie toe die een bestand met de BLOB maakt met behulp van een andere connector. U kunt bijvoorbeeld een OneDrive-actie toevoegen waarmee een bestand wordt gemaakt op basis van de inhoud van de blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Verbinding maken met het opslag account

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wanneer u wordt gevraagd om de verbinding te maken, geeft u de volgende informatie op:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | <*verbindings naam*> | De naam die voor uw verbinding moet worden gemaakt |
   | **Opslagaccount** | Yes | <*opslag account*> | Selecteer uw opslagaccount in de lijst. |
   ||||

   Bijvoorbeeld:

   ![Verbinding maken met Azure Blob Storage-account](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Wanneer u klaar bent, selecteert u **maken**

1. Nadat u de verbinding hebt gemaakt, gaat u door met het [toevoegen van Blob Storage-trigger](#add-trigger) of het [toevoegen van Blob Storage-actie](#add-action).

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/azureblobconnector/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Toegang tot opslag accounts achter firewalls

U kunt netwerk beveiliging toevoegen aan een Azure Storage-account door de toegang te beperken tot een [firewall en firewall regels](../storage/common/storage-network-security.md). Deze installatie maakt echter een uitdaging voor Azure en andere micro soft-services die toegang nodig hebben tot het opslag account. Lokale communicatie in het Data Center abstracten de interne IP-adressen, zodat u geen firewall regels met IP-beperkingen kunt instellen. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md) voor meer informatie.

Hier vindt u diverse opties voor toegang tot opslag accounts achter firewalls van Azure Logic Apps met behulp van de Azure Blob Storage-connector of andere oplossingen:

* Azure Storage Blob-connector

  * [Toegang tot opslag accounts in andere regio's](#access-other-regions)
  * [Toegang tot opslag accounts via een vertrouwd virtueel netwerk](#access-trusted-virtual-network)

* Andere oplossingen

  * [Opslag accounts openen als een vertrouwde service met beheerde identiteiten](#access-trusted-service)
  * [Toegang tot opslag accounts via Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemen met toegang tot opslag accounts in dezelfde regio

Logic apps hebben geen directe toegang tot opslag accounts achter firewalls wanneer ze zich in dezelfde regio bevinden. Als tijdelijke oplossing plaatst u uw logische apps in een regio die verschilt van uw opslag account en geeft u toegang tot de [uitgaande IP-adressen voor de beheerde connectors in uw regio](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Deze oplossing is niet van toepassing op de Azure Table Storage-connector en de Azure Queue Storage-connector. Gebruik in plaats daarvan de ingebouwde HTTP-trigger en acties om toegang te krijgen tot uw Table Storage of Queue Storage.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Toegang tot opslag accounts via een vertrouwd virtueel netwerk

U kunt het opslag account in een virtueel Azure-netwerk dat u beheert, plaatsen en dat virtuele netwerk vervolgens toevoegen aan de lijst met vertrouwde virtuele netwerken. Als u wilt dat uw logische app toegang krijgt tot het opslag account via een [vertrouwd virtueel netwerk](../virtual-network/virtual-networks-overview.md), moet u die logische app implementeren in een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), die verbinding kan maken met bronnen in een virtueel netwerk. U kunt vervolgens de subnetten in die ISE toevoegen aan de vertrouwde lijst. Azure Storage-connectors, zoals de Blob Storage-connector, hebben rechtstreeks toegang tot de opslag container. Deze instelling is dezelfde ervaring als het gebruik van de service-eind punten van een ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Opslag accounts openen als een vertrouwde service met beheerde identiteiten

Als u micro soft-vertrouwde services toegang wilt geven tot een opslag account via een firewall, kunt u een uitzonde ring voor dat opslag account instellen voor die services. Met deze oplossing kunnen Azure-Services die [beheerde identiteiten ondersteunen voor verificatie](../active-directory/managed-identities-azure-resources/overview.md) , toegang krijgen tot opslag accounts achter firewalls als vertrouwde services. Met name voor een logische app in een wereld wijde multi tenant Azure om toegang te krijgen tot deze opslag accounts, moet u de [ondersteuning voor beheerde identiteiten inschakelen](../logic-apps/create-managed-service-identity.md) voor de logische app. Vervolgens gebruikt u de HTTP-actie of-trigger in uw logische app en stelt u het [verificatie type in om de beheerde identiteit van uw logische app te gebruiken](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Voor dit scenario kunt u *alleen* de http-actie of trigger gebruiken.

Voer de volgende algemene stappen uit om de ondersteuning voor uitzonde ringen en beheerde identiteit in te stellen:

1. Selecteer in uw opslag account onder **instellingen** **firewalls en virtuele netwerken**. Selecteer onder **toegang toestaan vanaf** de optie **geselecteerde netwerken** zodat de gerelateerde instellingen worden weer gegeven.

1. Onder **uitzonde ringen** selecteert **u vertrouwde micro soft-Services toegang geven tot dit opslag account** en selecteert u vervolgens **Opslaan**.

   ![Selecteer een uitzonde ring die micro soft-vertrouwde services toestaat](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Schakel in de instellingen van de logische app [ondersteuning in voor de beheerde identiteit](../logic-apps/create-managed-service-identity.md).

1. Voeg in de werk stroom van de logische app de HTTP-actie of trigger toe en stel deze in om toegang te krijgen tot het opslag account of de entiteit.

   > [!IMPORTANT]
   > Voor uitgaande HTTP-actie-of trigger aanroepen naar Azure Storage-accounts, moet u ervoor zorgen dat de aanvraag header de `x-ms-version` eigenschap en de API-versie bevat voor de bewerking die u wilt uitvoeren op het opslag account. Zie [toegang verifiëren met beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) en [versie beheer voor Azure Storage services](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)voor meer informatie.

1. Selecteer bij die actie [de beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) die u voor verificatie wilt gebruiken.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Toegang tot opslag accounts via Azure API Management

Als u een toegewezen laag gebruikt voor [API Management](../api-management/api-management-key-concepts.md), kunt u de opslag-API vooraan gebruiken door gebruik te maken van API management en de IP-adressen van de laatste via de firewall toe te staan. Voeg in principe het virtuele Azure-netwerk toe dat wordt gebruikt door API Management aan de firewall instelling van het opslag account. U kunt vervolgens de API Management actie of de HTTP-actie gebruiken om de Azure Storage-Api's aan te roepen. Als u deze optie kiest, moet u echter zelf het verificatie proces afhandelen. Zie [eenvoudige architectuur voor ondernemings integratie](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)