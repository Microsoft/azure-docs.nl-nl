---
title: Systeem onderwerpen maken, weer geven en beheren in Azure Event Grid (Portal)
description: In dit artikel wordt beschreven hoe u een bestaand systeem onderwerp weergeeft, Azure Event Grid systeem onderwerpen maakt met behulp van de Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86115108"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Event Grid systeem onderwerpen maken, weer geven en beheren in de Azure Portal
In dit artikel leest u hoe u systeem onderwerpen maakt en beheert met behulp van de Azure Portal. Zie [systeem onderwerpen](system-topics.md)voor een overzicht van systeem onderwerpen.

## <a name="create-a-system-topic"></a>Een systeem onderwerp maken
U kunt op twee manieren een systeem onderwerp maken voor een Azure-resource (opslag account, Event Hubs naam ruimte, enzovoort):

- De pagina **gebeurtenissen** van een resource gebruiken, bijvoorbeeld opslag Account of event hubs naam ruimte. Wanneer u de pagina **gebeurtenissen** in de Azure Portal gebruikt voor het maken van een gebeurtenis abonnement voor een gebeurtenis die door een Azure-bron is gegenereerd (bijvoorbeeld: Azure Storage-account), maakt de portal een systeem onderwerp voor de Azure-resource en maakt vervolgens een abonnement voor het onderwerp System. U geeft de naam van het onderwerp van het systeem op als u voor de eerste keer een gebeurtenis abonnement op de Azure-resource maakt. Vanaf de tweede keer wordt de naam van het systeem onderwerp weer gegeven in de modus alleen-lezen. Zie [Quick Start: door sturen van Blob Storage-gebeurtenissen naar een webeindpunt met de Azure Portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) voor gedetailleerde stappen.
- Op de pagina met **Event grid systeem onderwerpen** . De volgende stappen zijn voor het maken van een systeem onderwerp met behulp van de pagina **Event grid systeem onderwerpen** . 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Typ **Event grid systeem onderwerpen** in het zoekvak bovenaan en druk op **Enter**. 

    ![Systeem onderwerpen zoeken](./media/create-view-manage-system-topics/search-system-topics.png)
3. Selecteer op de pagina **Event grid systeem onderwerpen** **+ toevoegen** op de werk balk.

    ![Systeem onderwerp toevoegen-werkbalk knop](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Voer de volgende stappen uit op de pagina **onderwerp Event grid systeem maken** :
    1. Selecteer het **type onderwerp**. In het volgende voor beeld is de optie **opslag accounts** geselecteerd. 
    2. Selecteer het **Azure-abonnement** met uw opslag account bron. 
    3. Selecteer de **resource groep** die het opslag account heeft. 
    4. Selecteer het **opslag account**. 
    5. Voer een **naam** in voor het systeem onderwerp dat moet worden gemaakt. 
    
        > [!NOTE]
        > U kunt de naam van het systeem onderwerp gebruiken om metrische gegevens en Diagnostische logboeken te zoeken.
    6. Selecteer **Controleren + maken**.

        ![Systeem onderwerp maken](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Controleer de instellingen en selecteer **maken**. 
        
        ![Een systeem onderwerp controleren en maken](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Nadat de implementatie is voltooid, selecteert u **naar resource gaan** om de pagina met **Event grid systeem onderwerpen** weer te geven voor het systeem onderwerp dat u hebt gemaakt. 

        ![Pagina systeem onderwerp](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Alle systeem onderwerpen weer geven
Volg deze stappen om alle bestaande Event Grid systeem onderwerpen weer te geven. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Typ **Event grid systeem onderwerpen** in het zoekvak bovenaan en druk op **Enter**. 

    ![Systeem onderwerpen zoeken](./media/create-view-manage-system-topics/search-system-topics.png)
3. Op de pagina **Event grid systeem onderwerpen** ziet u alle systeem onderwerpen. 

    ![Lijst met systeem onderwerpen](./media/create-view-manage-system-topics/list-system-topics.png)
4. Selecteer een **systeem onderwerp** in de lijst om de details ervan weer te geven. 

    ![Details van het systeem onderwerp](./media/create-view-manage-system-topics/system-topic-details.png)

    Op deze pagina vindt u informatie over het systeem onderwerp, zoals de volgende informatie: 
    - Bron. De naam van de resource waarop het systeem onderwerp is gemaakt.
    - Bron type. Het type van de resource. Bijvoorbeeld: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , enzovoort `Microsoft.Resources.ResourceGroups` .
    - Abonnementen die zijn gemaakt voor het onderwerp System.

    Op deze pagina kunnen bewerkingen zoals de volgende worden uitgevoerd:
    - Een gebeurtenis abonnement maken Selecteer **+ gebeurtenis abonnement** op de werk balk. 
    - Een gebeurtenis abonnement verwijderen. Selecteer **verwijderen** op de werk balk. 
    - Labels voor het onderwerp systeem toevoegen. Selecteer **labels** in het linkermenu en geef label namen en waarden op. 


## <a name="delete-a-system-topic"></a>Een systeem onderwerp verwijderen
1. Volg de instructies in de sectie [systeem onderwerpen weer geven](#view-all-system-topics) om alle systeem onderwerpen weer te geven en selecteer het onderwerp van het systeem dat u wilt verwijderen uit de lijst. 
2. Selecteer op de pagina **Event grid systeem onderwerp** **verwijderen** op de werk balk. 

    ![Systeem onderwerp-knop verwijderen](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Selecteer op de pagina Bevestiging **OK** om de verwijdering te bevestigen. Het onderwerp System en ook de gebeurtenis abonnementen voor het onderwerp System worden verwijderd.  

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken
1. Volg de instructies in de sectie [systeem onderwerpen weer geven](#view-all-system-topics) om alle systeem onderwerpen weer te geven en selecteer het onderwerp van het systeem dat u wilt verwijderen uit de lijst. 
2. Selecteer op de pagina **Event grid systeem onderwerp** **+ gebeurtenis abonnement** op de werk balk. 

    ![Systeem onderwerp: knop gebeurtenis abonnement toevoegen](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Controleer of het **onderwerp type**, **bron resource** en **onderwerpnaam** automatisch worden ingevuld. Voer een naam in, selecteer een **eindpunt type** en geef het **eind punt** op. Selecteer vervolgens **maken** om het gebeurtenis abonnement te maken. 

    ![Systeem onderwerp: gebeurtenis abonnement maken](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Volgende stappen
Zie de sectie [systeem onderwerpen in azure Event grid](system-topics.md) voor meer informatie over systeem onderwerpen en onderwerp typen die door Azure Event grid worden ondersteund. 
