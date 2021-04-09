---
title: Onboarding als een Azure Event Grid partner met behulp van Azure Portal
description: Gebruik Azure Portal om een Azure Event Grid partner onboarding te kunnen uitvoeren.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050949"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Onboarding als een Azure Event Grid partner met behulp van de Azure Portal
In dit artikel wordt beschreven hoe SaaS-providers van derden, ook wel bekend als gebeurtenis uitgevers of partners, worden voorEvent Grid om gebeurtenissen van hun services te kunnen publiceren en hoe deze gebeurtenissen door eind gebruikers worden verbruikt.

> [!IMPORTANT]
> Als u niet bekend bent met partner evenementen, raadpleegt u [overzicht van partner gebeurtenissen](partner-events-overview.md) voor een gedetailleerde inleiding in de belangrijkste concepten die essentieel zijn om te begrijpen en de stappen in dit artikel uit te voeren.

## <a name="onboarding-process-for-event-publishers-partners"></a>Onboarding-proces voor gebeurtenis uitgevers (partners)
In een kort gezegd is het mogelijk dat de gebeurtenissen van uw service worden gebruikt door gebruikers. Dit houdt doorgaans in het volgende proces:

1. **Communiceer uw interesse** om een partner te worden voor het event Grid Service team voordat u verdergaat met de volgende stappen.
1. Maak een type partner onderwerp door een **registratie** te maken. 
1. Een **naam ruimte** maken.
1. Een **gebeurtenis kanaal** en een **partner onderwerp** maken (één stap).
1. Test de functionaliteit van de partner gebeurtenissen end-to-end.

Voor stap #4 moet u bepalen welk soort gebruikers ervaring u wilt bieden. U hebt de volgende opties:
- Geef uw eigen oplossing, meestal een web Graphical User Interface (GUI)-ervaring, die wordt gehost onder uw domein met onze SDK en/of REST API om een gebeurtenis kanaal en het bijbehorende partner onderwerp te maken. Met deze optie kunt u de gebruiker vragen om het abonnement en de resource groep waaronder u een partner onderwerp gaat maken.
- Gebruik Azure Portal of CLI om het gebeurtenis kanaal en het bijbehorende partner onderwerp te maken. Met deze optie moet u in het Azure-abonnement van de gebruiker op een of andere manier beschikken over de resource groep waaronder u een partner onderwerp gaat maken. 

In dit artikel wordt beschreven hoe u kunt voorbereiden als Azure Event Grid partner met behulp van de Azure Portal. 

> [!NOTE]
> Het registreren van een type partner onderwerp is een optionele stap. Zie resources die worden [beheerd door de uitgever van gebeurtenissen](partner-events-overview.md#resources-managed-by-event-publishers)om te bepalen of u een type partner onderwerp moet maken.

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Communiceer uw interesse om een partner te worden
Vul [dit formulier](https://aka.ms/gridpartnerform) in en neem contact op met het event grid team op [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . We hebben een gesprek met u gedetailleerde informatie over de use cases van partner Events, personen, het voorbereidings proces, de functionaliteit, de prijzen en nog veel meer.

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u over het volgende beschikt om de resterende stappen te volt ooien:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een Azure- [Tenant](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>Een type partner onderwerp registreren (optioneel)
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **alle services** in het navigatie deel venster links en typ **Event grid partner registraties** in de zoek balk en selecteer deze. 
1. Selecteer op de pagina **Event grid partner registraties** **+ toevoegen** op de werk balk. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Koppeling voor partner registratie toevoegen":::
1. Voer de volgende gegevens in op de pagina **registraties van het type partner-onderwerp maken** : 
    1. Voer de volgende stappen uit in de sectie **Project Details** :
        1. Selecteer uw Azure-**abonnement**. 
        1. Selecteer een bestaande Azure- **resource groep** of maak een nieuwe resource groep. 
    1. Voer de volgende stappen uit in de sectie **registratie Details** :
        1. Voer bij **registratie naam** een naam in voor de registratie. 
        1. Voer de naam van uw organisatie in bij **organisatie naam**. 
    1. Voer in het gedeelte **partner resource type** Details in over het resource type dat wordt weer gegeven op de pagina voor het maken van een **partner onderwerp** : 
        1. Voer de naam van het resource type in voor de naam van het **bron type** van de partner. Dit is het type partner onderwerp dat wordt gemaakt in uw Azure-abonnement. 
        2. Voer voor **weergave naam** een gebruiks vriendelijke weergave naam in voor het type partner onderwerp (resource). 
        3. Voer een **beschrijving in voor het resource type**. 
        4. Voer een **beschrijving in voor het scenario**. Hierin worden de manieren of scenario's uitgelegd waarin de partner onderwerpen voor uw resources kunnen worden gebruikt.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Partner registratie maken":::            
1. Selecteer **volgende: aangepaste service** onder aan de pagina. Voer op het tabblad **Customer Service** van de pagina **partner registratie maken** de gegevens in die door de abonnee server worden gebruikt om contact met u op te nemen in het geval van een probleem met de bron van de gebeurtenis:
    1. Voer het **telefoon nummer** in.
    1. Voer een **extensie** in voor het telefoon nummer.
    1. Voer een ondersteunings website- **URL** in. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Partner registratie maken-klanten service":::        
1. Selecteer **volgende: Labels** aan de onderkant van de pagina. 
1. Configureer op de pagina **Tags** de volgende waarden. 
    1. Voer een **naam** en een **waarde** in voor de tag die u wilt toevoegen. Deze stap is **optioneel**. 
    1. Selecteer onder aan de pagina **controleren + maken** om de registratie te maken (type partner onderwerp).

## <a name="create-a-partner-namespace"></a>Een partner naam ruimte maken

1. Selecteer in het Azure Portal **alle services** in het navigatie menu links en typ **Event grid partner naam ruimten** in de zoek balk en selecteer deze in de lijst. 
1. Selecteer op de pagina **naam ruimten van Event grid** **+ toevoegen** op de werk balk. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Partner naam ruimten-koppeling toevoegen":::
1. Geef op de pagina **partner naam ruimte maken-basis beginselen** de volgende informatie op.
    1. Voer de volgende stappen uit in de sectie **Project Details** : 
        1. Selecteer een Azure-**abonnement**.
        1. Selecteer een bestaande **resource groep** of maak een resource groep. 
    1. Voer de volgende stappen uit in de sectie **naam ruimte Details** :
        1. Voer een **naam** in voor de naamruimte. 
        1. Selecteer een **locatie** voor de naamruimte. 
    1. Voer in de sectie **registratie Details** de volgende stappen uit om de naam ruimte te koppelen aan een partner registratie. 
        1. Selecteer het **abonnement** waarin de partner registratie bestaat. 
        1. Selecteer de **resource groep** die de partner registratie bevat. 
        1. Selecteer de **registratie** van de partner in de vervolg keuzelijst.
    1. Selecteer **volgende: Labels** aan de onderkant van de pagina.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Partner naam ruimte maken-pagina basis beginselen":::
1. Voeg op de pagina **Tags** tags toe (optioneel).
    1. Voer een **naam** en een **waarde** in voor de tag die u wilt toevoegen. Deze stap is **optioneel**.
    1. Selecteer **Controleren en maken** onderaan de pagina.         
1. Controleer de details op de pagina **controleren en maken** en selecteer **maken**. 

## <a name="create-an-event-channel"></a>Een gebeurtenis kanaal maken
> [!IMPORTANT]
> U moet uw gebruiker een Azure-abonnement, resource groep, locatie en naam van het partner onderwerp aanvragen om een partner onderwerp te maken dat uw gebruiker bezit en beheert.

1. Ga naar de pagina **overzicht** van de naam ruimte die u hebt gemaakt. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Partner naam ruimte-overzichts pagina":::
    partner-namespace-overview.png
1. Selecteer **+ Event Channel** op de werk balk. 
1. Geef op de pagina **gebeurtenis kanaal maken-basis beginselen** de volgende informatie op. 
    1. Voer in de sectie **kanaal Details** de volgende stappen uit:
        1. Voer bij **gebeurtenis kanaal naam** een naam in voor het gebeurtenis kanaal. 
        1. Voer de **bron** in. Zie [Cloud Events 1,0-specificaties](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) om een idee te krijgen van een geschikte waarde voor de bron. Zie ook [Dit schema voor Cloud gebeurtenissen](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. Voer in de sectie **doel Details** Details in voor het doel partner onderwerp dat voor dit gebeurtenis kanaal wordt gemaakt. 
        1. Voer de **id in van het abonnement** waarin het partner onderwerp wordt gemaakt. 
        1. Voer de **naam in van de resource groep** waarin de bron van het partner onderwerp wordt gemaakt. 
        1. Voer een **naam in voor het partner onderwerp**. 
    1. Selecteer **volgende: filters** onder aan de pagina. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Gebeurtenis kanaal maken-pagina basis beginselen":::
1. Voeg filters toe op de pagina **filters** . moet u de volgende stappen uitvoeren:
    1. Filter op kenmerken van elke gebeurtenis. Alleen gebeurtenissen die overeenkomen met alle filters, worden geleverd. U kunt Maxi maal 25 filters opgeven. Vergelijkingen zijn niet hoofdletter gevoelig. Geldige sleutels die worden gebruikt voor filters variëren op basis van het gebeurtenis schema. In het volgende voor beeld,,, `eventid` `source` `eventtype` en `eventtypeversioin` kan worden gebruikt voor sleutels. U kunt ook aangepaste eigenschappen binnen de gegevens lading gebruiken met behulp van de `.` operator voor nesten. Bijvoorbeeld: `data` , `data.key` , `data.key1.key2` .
    1. Selecteer **volgende: extra functies** onder aan de pagina. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Gebeurtenis kanaal maken-pagina filters":::
        create-event-channel-filters-page.png
1. Op de pagina **extra functies** kunt u een **verval tijd** en een **beschrijving voor het partner onderwerp** instellen. 
    1. De **verloop tijd** is de tijd waarop het onderwerp en het bijbehorende gebeurtenis kanaal automatisch worden verwijderd als dit niet door de klant wordt geactiveerd. Een standaard waarde van zeven dagen wordt gebruikt wanneer er geen tijd wordt gegeven. Schakel het selectie vakje in om uw eigen verloop tijd op te geven. 
    1. Omdat dit onderwerp een resource is die niet door de gebruiker is gemaakt, kan een **Beschrijving** de gebruiker helpen bij het leren van de aard van dit onderwerp. Er wordt een algemene beschrijving gegeven als er geen is ingesteld. Schakel het selectie vakje in om een beschrijving van uw eigen partner onderwerp in te stellen. 
    1. Selecteer **Volgende: Beoordelen en maken**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Gebeurtenis kanaal maken-pagina aanvullende functies":::
1. Controleer de instellingen op de weer **geven + maken** en selecteer **maken** om het gebeurtenis kanaal te maken. 

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van de onderwerpen over partners](./partner-events-overview.md)
- [Formulier voor het voorbereiden van partner onderwerpen](https://aka.ms/gridpartnerform)
- [Auth0-partner onderwerp](auth0-overview.md)
- [Het Auth0-partner onderwerp gebruiken](auth0-how-to.md)