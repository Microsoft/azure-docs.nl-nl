---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606950"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Een naamruimte in Azure Portal maken
Als u Service Bus-berichtenentiteiten wilt gebruiken in Azure, moet u eerst een naamruimte maken met een naam die uniek is binnen Azure. Een naamruimte biedt een scoping container voor het verwerken van Service Bus-resources in uw toepassing.

Ga als volgt te werk om een naamruimte te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
2. Klik in het linkernavigatievenster van de portal achtereenvolgens op **+ Een resource maken**, **Integratie** en **Service Bus**.

    ![Een resource maken -> Integratie -> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. Voer in het dialoogvenster **Naamruimte maken** de volgende stappen uit: 
    1. Voer een **naam in voor de naamruimte**. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is. Raadpleeg [Create Namespace](/rest/api/servicebus/create-namespace) (Naamruimte maken) voor een lijst met regels voor het benoemen van naamruimtes.
    2. Selecteer de prijscategorie (Basic, Standard of Premium) voor de naamruimte. Kies Standard of Premium indien u gebruik wilt maken van [onderwerpen en abonnementen](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions). Onderwerpen/abonnementen worden niet ondersteund in de prijscategorie Basic.
    3. Als u de **Premium** prijscategorie hebt geselecteerd, gaat u als volgt te werk: 
        1. Geef het aantal **Messaging-eenheden** op. De Premium-prijscategorie biedt isolatie van resources op het niveau van de CPU en het geheugen, zodat elke workload geïsoleerd wordt uitgevoerd. Deze resourcecontainer wordt een Messaging-eenheid genoemd. Een Premium-naamruimte heeft ten minste één Messaging-eenheid. U kunt voor elke Service Bus Premium-naamruimte 1, 2 of 4 Messaging-eenheden selecteren. Zie [Service Bus Premium Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md) voor meer informatie.
        2. Geef aan of u de naamruimte **zone-redundant** wilt maken. Door zone-redundantie wordt de beschikbaarheid verbeterd door replica's te verspreiden over verschillende beschikbaarheidszones binnen één regio, zonder aanvullende kosten. Zie [Beschikbaarheidszones in Azure](../articles/availability-zones/az-overview.md) voor meer informatie.
    4. Kies voor **Abonnement** een Azure-abonnement waarin u de naamruimte maakt.
    5. Kies voor **Resourcegroep** een bestaande resourcegroep waarin de naamruimte moet worden opgenomen of maak een nieuwe resourcegroep.      
    6. Kies voor **Locatie** de regio waarin uw naamruimte moet worden gehost.
    7. Selecteer **Maken**. Uw naamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.
   
        ![Een naamruimte maken](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Bevestig dat de Service Bus-naamruimte is geïmplementeerd. Als u de meldingen wilt zien, selecteert u het **Klokpictogram (waarschuwingen)** op de werkbalk. Selecteer de **naam van de resourcegroep** in de melding zoals weergegeven in de afbeelding. U ziet de resourcegroep die de Service Bus-naamruimte bevat.

    ![Implementatiemelding](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Selecteer op de pagina **Resourcegroep** voor de resourcegroep uw **Service Bus-naamruimte**. 

    ![Pagina Resourcegroep: selecteer uw Service Bus-naamruimte](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. U ziet de startpagina voor uw Service Bus-naamruimte. 

    ![Startpagina voor uw Service Bus-naamruimte](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen 
Bij het maken van een nieuwe naamruimte wordt automatisch een eerste SAS-regel (Shared Access Signature) gegenereerd met een bijbehorende primaire en secundaire sleutel die elk volledige controle over alle aspecten van de naamruimte bieden. Raadpleeg [Service Bus-verificatie en -autorisatie](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) voor meer informatie over het maken van regels met beperktere rechten voor reguliere afzenders en ontvangers. Volg deze stappen voor het kopiëren van de primaire en secundaire sleutels voor de naamruimte: 

1. Klik op **Alle resources** en klik vervolgens op de zojuist gemaakte naam voor de naamruimte.
2. Klik in het venster van de naamruimte op **Beleid voor gedeelde toegang**.
3. Klik in het scherm **Beleid voor gedeelde toegang** op **RootManageSharedAccessKey**.
   
    ![Schermopname toont het venster met het beleid voor gedeelde toegang waarbij een beleidsregel gemarkeerd is.](./media/service-bus-create-namespace-portal/connection-info.png)
4. Klik in het venster **Beleid: RootManageSharedAccessKey** op de knop Kopiëren naast **Primaire verbindingsreeks** om de verbindingsreeks naar het klembord te kopiëren voor later gebruik. Plak deze waarde in Kladblok of een andere tijdelijke locatie.
   
    ![Schermopname toont het S A S-beleid RootManageSharedAccessKey, dat sleutels en verbindingsreeksen omvat.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Herhaal de vorige stap: het kopiëren en plakken van de waarde voor de **Primaire sleutel** voor een tijdelijke locatie zodat u deze later kunt gebruiken.

<!--Image references-->

