---
title: Aangepaste Azure-rollen maken of bijwerken met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het maken van aangepaste rollen in azure met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Hieronder vindt u informatie over het weer geven, maken, bijwerken en verwijderen van aangepaste rollen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369223"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Aangepaste Azure-rollen maken of bijwerken met behulp van Azure Portal

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste Azure-rollen maken. Net als bij ingebouwde rollen kunt u aangepaste rollen toewijzen aan gebruikers, groepen en service-principals bij een abonnement en een resource groep. Aangepaste rollen worden opgeslagen in een Azure Active Directory-Directory (Azure AD) en kunnen worden gedeeld via abonnementen. Elke directory kan Maxi maal 5000 aangepaste rollen hebben. Aangepaste rollen kunnen worden gemaakt met behulp van de Azure Portal, Azure PowerShell, Azure CLI of de REST API. In dit artikel wordt beschreven hoe u aangepaste rollen maakt met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Stap 1: de machtigingen bepalen die u nodig hebt

Azure heeft duizenden machtigingen die u mogelijk in uw aangepaste rol kunt gebruiken. Hier volgen enkele methoden die u kunnen helpen bij het bepalen van de machtigingen die u wilt toevoegen aan uw aangepaste rol:

- Bekijk de bestaande [ingebouwde rollen](built-in-roles.md).
- Vermeld de Azure-Services waaraan u toegang wilt verlenen.
- Bepaal de [resource providers die zijn toegewezen aan de Azure-Services](../azure-resource-manager/management/azure-services-resource-providers.md). Een zoek methode wordt verderop in [stap 4: machtigingen](#step-4-permissions)beschreven.
- Zoek de [beschik bare machtigingen](resource-provider-operations.md) om de machtigingen te vinden die u wilt toevoegen. Een zoek methode wordt verderop in [stap 4: machtigingen](#step-4-permissions)beschreven.

## <a name="step-2-choose-how-to-start"></a>Stap 2: kiezen hoe u begint

Er zijn drie manieren waarop u kunt beginnen met het maken van een aangepaste rol. U kunt een bestaande functie klonen, helemaal beginnen of beginnen met een JSON-bestand. De eenvoudigste manier is om een bestaande rol te vinden die de meeste machtigingen heeft die u nodig hebt en deze vervolgens te klonen en te wijzigen voor uw scenario. 

### <a name="clone-a-role"></a>Een rol klonen

Als een bestaande rol niet beschikt over de machtigingen die u nodig hebt, kunt u deze klonen en vervolgens de machtigingen wijzigen. Volg deze stappen om te beginnen met het klonen van een rol.

1. Open in de Azure Portal een abonnement of resource groep waaraan u de aangepaste rol wilt toewijzen en open vervolgens **toegangs beheer (IAM)**.

    Op de volgende scherm afbeelding ziet u de IAM-pagina (Access Control) die voor een abonnement is geopend.

    ![De pagina toegangs beheer (IAM) voor een abonnement](./media/custom-roles-portal/access-control-subscription.png)

1. Klik op het tabblad **rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

1. Zoek naar een rol die u wilt klonen, zoals de rol facturerings lezer.

1. Klik aan het einde van de rij op het weglatings teken (**...**) en klik vervolgens op **klonen**.

    ![Context menu klonen](./media/custom-roles-portal/clone-menu.png)

    Hiermee opent u de editor voor aangepaste rollen met de optie **een rol klonen** geselecteerd.

1. Ga verder met [stap 3: basis principes](#step-3-basics).

### <a name="start-from-scratch"></a>De procedure vanaf het begin uitvoeren

Als u wilt, kunt u deze stappen volgen om een nieuwe aangepaste rol te starten.

1. Open in de Azure Portal een abonnement of resource groep waaraan u de aangepaste rol wilt toewijzen en open vervolgens **toegangs beheer (IAM)**.

1. Klik op **toevoegen** en klik vervolgens op **aangepaste rol toevoegen**.

    ![Menu aangepaste rol toevoegen](./media/custom-roles-portal/add-custom-role-menu.png)

    Hiermee opent u de editor voor aangepaste rollen met de optie **starten vanuit het begin** .

1. Ga verder met [stap 3: basis principes](#step-3-basics).

### <a name="start-from-json"></a>Starten vanuit JSON

Als u wilt, kunt u het meren deel van uw aangepaste Rolinstellingen opgeven in een JSON-bestand. U kunt het bestand openen in de editor voor aangepaste rollen, extra wijzigingen aanbrengen en vervolgens de aangepaste rol maken. Volg deze stappen om te beginnen met een JSON-bestand.

1. Maak een JSON-bestand met de volgende indeling:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. Geef in het JSON-bestand waarden op voor de verschillende eigenschappen. Hier volgt een voor beeld met een aantal toegevoegde waarden. Zie [definities van Azure-functies begrijpen](role-definitions.md)voor meer informatie over de verschillende eigenschappen.

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Open de pagina **toegangs beheer (IAM)** In het Azure Portal.

1. Klik op **toevoegen** en klik vervolgens op **aangepaste rol toevoegen**.

    ![Menu aangepaste rol toevoegen](./media/custom-roles-portal/add-custom-role-menu.png)

    Hiermee opent u de editor voor aangepaste rollen.

1. Selecteer op het tabblad basis beginselen in **basislijn machtigingen** de optie **starten vanuit JSON**.

1. Klik naast het vak een bestand selecteren op de knop map om het dialoog venster openen te openen.

1. Selecteer het JSON-bestand en klik vervolgens op **openen**.

1. Ga verder met [stap 3: basis principes](#step-3-basics).

## <a name="step-3-basics"></a>Stap 3: basis beginselen

Op het tabblad **basis beginselen** geeft u de naam, de beschrijving en de basislijn machtigingen voor uw aangepaste rol op.

1. Geef in het vak **naam van aangepaste rol** een naam op voor de aangepaste rol. De naam moet uniek zijn voor de Azure AD-adres lijst. De naam kan letters, cijfers, spaties en speciale tekens bevatten.

1. Geef in het vak **Beschrijving** een optionele beschrijving op voor de aangepaste rol. Dit wordt de knop Info voor de aangepaste rol.

    De optie **machtigingen voor basis lijn** moet al zijn ingesteld op basis van de vorige stap, maar u kunt wijzigen.

    ![Tabblad basis gegevens met opgegeven waarden](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Stap 4: machtigingen

Op het tabblad **machtigingen** geeft u de machtigingen voor uw aangepaste rol op. Afhankelijk van of u een rol hebt gekloond of als u bent begonnen met JSON, worden er mogelijk al een aantal machtigingen weer geven op het tabblad Machtigingen.

![Tabblad Machtigingen van aangepaste rol maken](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Machtigingen toevoegen of verwijderen

Volg deze stappen om machtigingen toe te voegen aan of te verwijderen voor uw aangepaste rol.

1. Om machtigingen toe te voegen, klikt u op **machtigingen toevoegen** om het deel venster machtigingen toevoegen te openen.

    In dit deel venster worden alle beschik bare machtigingen weer gegeven die zijn gegroepeerd in verschillende categorieën in een kaart indeling. Elke categorie vertegenwoordigt een *resource provider*, een service die Azure-resources levert.

1. Typ in het vak **zoeken naar een machtiging** een teken reeks om te zoeken naar machtigingen. Zoek bijvoorbeeld naar de *factuur* om machtigingen te vinden die betrekking hebben op de factuur.

    Er wordt een lijst van resource provider kaarten weer gegeven op basis van uw zoek reeks. Zie [resource providers voor Azure-Services](../azure-resource-manager/management/azure-services-resource-providers.md)voor een lijst met de manier waarop resource providers aan Azure-Services worden toegewezen.

    ![Deel venster machtigingen toevoegen met resource provider](./media/custom-roles-portal/add-permissions-provider.png)

1. Klik op een resource provider kaart die mogelijk de machtigingen heeft die u wilt toevoegen aan uw aangepaste rol, zoals **micro soft-facturering**.

    Er wordt een lijst met beheer machtigingen voor de resource provider weer gegeven op basis van uw zoek reeks.

    ![Lijst met machtigingen toevoegen](./media/custom-roles-portal/add-permissions-list.png)

1. Als u op zoek bent naar machtigingen die van toepassing zijn op het gegevens vlak, klikt u op **gegevens acties**. Als dat niet het geval is **, moet u** de acties in-en uitschakelen in de lijst met machtigingen die van toepassing zijn op het beheer vlak. Zie [beheer-en gegevens bewerkingen](role-definitions.md#management-and-data-operations)voor meer informatie over de verschillen tussen het beheer vlak en het gegevens vlak.

1. Als dat nodig is, werkt u de zoek teken reeks bij om uw zoek opdracht verder te verfijnen.

1. Als u een of meer machtigingen hebt gevonden die u wilt toevoegen aan uw aangepaste rol, moet u een vinkje naast de machtigingen toevoegen. Voeg bijvoorbeeld een vinkje naast **andere toe: factuur downloaden** om de machtiging voor het downloaden van facturen toe te voegen.

1. Klik op **toevoegen** om de machtiging toe te voegen aan uw lijst met machtigingen.

    De machtiging wordt toegevoegd als een `Actions` of een `DataActions` .

    ![Machtigingen toegevoegd](./media/custom-roles-portal/permissions-list-add.png)

1. Als u machtigingen wilt verwijderen, klikt u op het pictogram verwijderen aan het einde van de rij. In dit voor beeld, omdat een gebruiker geen ondersteunings tickets hoeft te maken, `Microsoft.Support/*` kan de machtiging worden verwijderd.

### <a name="add-wildcard-permissions"></a>Machtigingen voor joker tekens toevoegen

Afhankelijk van hoe u ervoor hebt gekozen om te beginnen, hebt u mogelijk machtigingen met Joker tekens ( `*` ) in de lijst met machtigingen. Een Joker teken ( `*` ) breidt een machtiging uit voor alles die overeenkomt met de actie teken reeks die u opgeeft. De volgende Joker teken reeks voegt bijvoorbeeld alle machtigingen toe die betrekking hebben op Azure Cost Management en exports. Dit omvat ook toekomstige export machtigingen die kunnen worden toegevoegd.

```
Microsoft.CostManagement/exports/*
```

Als u een nieuwe machtiging voor joker tekens wilt toevoegen, kunt u deze niet toevoegen met behulp van het deel venster **machtigingen toevoegen** . Als u een Joker machtiging wilt toevoegen, moet u deze hand matig toevoegen via het tabblad **JSON** . Zie [stap 6: JSON](#step-6-json)voor meer informatie.

### <a name="exclude-permissions"></a>Machtigingen uitsluiten

Als uw rol een machtiging voor joker tekens ( `*` ) heeft en u specifieke machtigingen uit die Joker machtiging wilt uitsluiten of verwijderen, kunt u deze uitsluiten. Stel bijvoorbeeld dat u de volgende Joker machtiging hebt:

```
Microsoft.CostManagement/exports/*
```

Als u niet wilt toestaan dat een export wordt verwijderd, kunt u de volgende machtiging voor verwijderen uitsluiten:

```
Microsoft.CostManagement/exports/delete
```

Wanneer u een machtiging uitsluit, wordt deze toegevoegd als een `NotActions` of `NotDataActions` . De juiste beheer machtigingen worden berekend door alle toe te voegen `Actions` en vervolgens alle te verwijderen `NotActions` . De juiste gegevens machtigingen worden berekend door alle toe te voegen `DataActions` en vervolgens alle te verwijderen `NotDataActions` .

> [!NOTE]
> Het uitsluiten van een machtiging is niet hetzelfde als een weigering. Het uitsluiten van machtigingen is een handige manier om machtigingen van een Joker machtiging af te trekken.

1. Als u een machtiging wilt uitsluiten of aftrekken van een toegestane Joker machtiging, klikt u op **machtigingen uitsluiten** om het deel venster uitsluitings machtigingen te openen.

    In dit deel venster geeft u de beheer-of gegevens machtigingen op die worden uitgesloten of afgetrokken.

1. Als u een of meer machtigingen hebt gevonden die u wilt uitsluiten, voegt u een vinkje naast de machtigingen toe en klikt u op de knop **toevoegen** .

    ![Deel venster machtigingen uitsluiten: machtiging geselecteerd](./media/custom-roles-portal/exclude-permissions-select.png)

    De machtiging wordt toegevoegd als een `NotActions` of `NotDataActions` .

    ![Uitgesloten machtiging](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Stap 5: toewijs bare bereiken

Op het tabblad **toewijs bare bereiken** geeft u op waar uw aangepaste rol beschikbaar is voor toewijzing, zoals abonnement of resource groep. Afhankelijk van hoe u ervoor hebt gekozen om te starten, kan op dit tabblad het bereik worden weer gegeven waar u de IAM-pagina (Access Control) hebt geopend. Het instellen van een toegewezen bereik aan het hoofd bereik (/) wordt niet ondersteund. Op dit moment kunt u geen beheer groep toevoegen als een toewijsbaar bereik.

1. Klik op **toewijs bare bereiken toevoegen** om het deel venster toewijs bare scopes toevoegen te openen.

    ![Tabblad toewijs bare bereiken](./media/custom-roles-portal/assignable-scopes.png)

1. Klik op een of meer bereiken die u wilt gebruiken, meestal uw abonnement.

    ![Toewijs bare bereiken toevoegen](./media/custom-roles-portal/add-assignable-scopes.png)

1. Klik op de knop **toevoegen** om uw toewijs bare bereik toe te voegen.

## <a name="step-6-json"></a>Stap 6: JSON

Op het tabblad **JSON** ziet u de aangepaste rol die is ingedeeld in JSON. Als u wilt, kunt u de JSON rechtstreeks bewerken. Als u een Joker teken ()- `*` machtiging wilt toevoegen, moet u dit tabblad gebruiken.

1. Klik op **bewerken** om de JSON te bewerken.

    ![Tabblad JSON met aangepaste rol](./media/custom-roles-portal/json.png)

1. Breng wijzigingen aan in de JSON.

    Als de JSON niet op de juiste wijze is geformatteerd, ziet u een rode gekartelde lijn en een indicator in de verticale rugmarge.

1. Wanneer u klaar bent met bewerken, klikt u op **Opslaan**.

## <a name="step-7-review--create"></a>Stap 7: controleren en maken

Op het tabblad **controleren en maken** kunt u de instellingen van uw aangepaste rol controleren.

1. Controleer de instellingen van uw aangepaste rol.

    ![Tabblad Beoordelen en maken](./media/custom-roles-portal/review-create.png)

1. Klik op **maken** om uw aangepaste rol te maken.

    Na enkele ogen blikken wordt een bericht weer gegeven waarin staat dat de aangepaste rol is gemaakt.

    ![Aangepast bericht van rol maken](./media/custom-roles-portal/custom-role-success.png)

    Als er fouten worden gedetecteerd, wordt er een bericht weer gegeven.

    ![Beoordeling + fout maken](./media/custom-roles-portal/review-create-error.png)

1. Bekijk de nieuwe aangepaste rol in de lijst **rollen** . Als u uw aangepaste rol niet ziet, klikt u op **vernieuwen**.

     Het kan een paar minuten duren voordat uw aangepaste rol overal wordt weer gegeven.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Volg deze stappen om uw aangepaste rollen weer te geven.

1. Open een abonnement of resource groep en open vervolgens **toegangs beheer (IAM)**.

1. Klik op het tabblad **rollen** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

1. Selecteer **CustomRole** in de lijst **type** om uw aangepaste rollen te bekijken.

    Als u zojuist uw aangepaste rol hebt gemaakt en u deze niet in de lijst ziet, klikt u op **vernieuwen**.

    ![Aangepaste rollen lijst](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

1. Zoals eerder in dit artikel is beschreven, opent u de lijst met aangepaste rollen.

1. Klik op het weglatings teken (**...**) voor de aangepaste rol die u wilt bijwerken en klik vervolgens op **bewerken**. Houd er rekening mee dat u ingebouwde rollen niet kunt bijwerken.

    De aangepaste rol wordt geopend in de editor.

    ![Menu aangepaste rol](./media/custom-roles-portal/edit-menu.png)

1. Gebruik de verschillende tabbladen om de aangepaste rol bij te werken.

1. Wanneer u klaar bent met uw wijzigingen, klikt u op het tabblad **controleren + maken** om uw wijzigingen te controleren.

1. Klik op de knop **bijwerken** om uw aangepaste rol bij te werken.

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

1. Zoals eerder in dit artikel is beschreven, opent u de lijst met aangepaste rollen.

1. Verwijder eventuele roltoewijzingen die gebruikmaken van de aangepaste rol.

1. Klik op het weglatings teken (**...**) voor de aangepaste rol die u wilt verwijderen en klik vervolgens op **verwijderen**.

    ![Scherm afbeelding met een lijst met aangepaste rollen die kunnen worden geselecteerd voor verwijdering.](./media/custom-roles-portal/delete-menu.png)

    Het kan een paar minuten duren voordat de aangepaste rol volledig is verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Azure-resource provider bewerkingen](resource-provider-operations.md)
