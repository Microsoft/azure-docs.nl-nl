---
title: Een exemplaar en verificatie instellen (portal)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen met behulp van de Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8263e0805f48976222e66922be8c04e0902101d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201835"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie (Portal) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van de Azure Portal. De Azure-portal is een geïntegreerde web-console die een alternatief vormt voor opdrachtregelprogramma's.
* Als u deze stappen hand matig wilt door lopen met de CLI, raadpleegt u de CLI-versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (CLI)*](how-to-set-up-instance-cli.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van de [Azure Portal](https://ms.portal.azure.com/). Ga naar de portal en meld u aan met uw referenties.

In de portal begint u met het selecteren van _een resource maken_ in het menu Start pagina van Azure Services.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecteren van ' een resource maken ' op de start pagina van de Azure Portal":::

Zoek in het zoekvak naar *Azure Digital apparaatdubbels* en kies de **Azure Digital apparaatdubbels** -service in de resultaten. Selecteer de knop _maken_ om een nieuw exemplaar van de service te maken.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="' Maken ' selecteren op de pagina van de Azure Digital Apparaatdubbels-service":::

Vul op de pagina de volgende **resource maken** de waarden in die hieronder zijn opgegeven:
* **Abonnement**: het Azure-abonnement dat u gebruikt
  - **Resource groep**: een resource groep waarin het exemplaar moet worden geïmplementeerd. Als u nog geen bestaande resource groep hebt, kunt u hier een maken door de *nieuwe koppeling maken* te selecteren en een naam in te voeren voor een nieuwe resource groep.
* **Locatie**: een Azure Digital apparaatdubbels-regio voor de implementatie. Ga voor meer informatie over regionale ondersteuning naar [*Azure-producten die beschikbaar zijn per regio (Azure Digital apparaatdubbels)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Resource naam**: een naam voor uw Azure Digital apparaatdubbels-exemplaar. Als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio waarin de opgegeven naam al wordt gebruikt, wordt u gevraagd een andere naam te kiezen.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="De beschreven waarden invullen om een Azure Digital Apparaatdubbels-resource te maken":::

Wanneer u klaar bent, kunt u **bekijken en maken** selecteren als u geen instellingen meer wilt configureren voor uw exemplaar. Hiermee gaat u naar een overzichts pagina waar u de exemplaar gegevens kunt bekijken die u hebt ingevoerd en met **maken** voltooit. 

Als u meer informatie wilt configureren voor uw exemplaar, worden in de volgende sectie de resterende installatie tabbladen beschreven.

### <a name="additional-setup-options"></a>Aanvullende installatie opties

Hier vindt u de aanvullende opties die u tijdens de installatie kunt configureren, met behulp van de andere tabbladen in het proces **resource maken** .

* **Netwerken**: op dit tabblad kunt u privé-eind punten inschakelen met [persoonlijke Azure-koppelingen](../private-link/private-link-overview.md) om de bloot stelling van open bare netwerken te elimineren voor uw exemplaar. Zie [*How to: private Access inschakelen with private link (preview)*](./how-to-enable-private-link-portal.md#add-a-private-endpoint-during-instance-creation)voor instructies.
* **Geavanceerd**: op dit tabblad kunt u een door een [systeem beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor uw exemplaar die kan worden gebruikt bij het door sturen van gebeurtenissen naar [eind punten](concepts-route-events.md). Zie [*How-to: Managed Identities inschakelen voor routerings gebeurtenissen (preview)*](./how-to-enable-managed-identities-portal.md#add-a-system-managed-identity-during-instance-creation)voor instructies.
* **Tags**: op dit tabblad kunt u Tags toevoegen aan uw instantie om deze te organiseren onder uw Azure-resources. Zie voor meer informatie over Azure-resource tags [*Label resources, resource groepen en abonnementen voor logische organisatie*](../azure-resource-manager/management/tag-resources.md).

### <a name="verify-success-and-collect-important-values"></a>Geslaagde pogingen controleren en belang rijke waarden verzamelen

Nadat u de installatie van het exemplaar hebt voltooid door **maken** te selecteren, kunt u de status van de implementatie van uw exemplaar bekijken in uw Azure-meldingen in de portal-pictogram balk. De melding geeft aan wanneer de implementatie is voltooid en u kunt de knop _Ga naar resource_ selecteren om uw gemaakte exemplaar weer te geven.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Weer gave van meldingen van Azure met een geslaagde implementatie en het markeren van de knop ' go to resource '":::

Als de implementatie mislukt, wordt de melding ook aangegeven waarom. Bekijk het advies van het fout bericht en probeer het exemplaar opnieuw te maken.

>[!TIP]
>Als uw exemplaar eenmaal is gemaakt, kunt u op elk gewenst moment teruggaan naar de pagina door te zoeken naar de naam van uw instantie in de zoek balk van Azure Portal.

Noteer de *naam*, de *resource groep* en de *hostnaam* op de pagina *overzicht* van het exemplaar. Dit zijn alle belang rijke waarden die u mogelijk nodig hebt om te werken met uw Azure Digital Apparaatdubbels-exemplaar. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markeren van de belang rijke waarden van de overzichts pagina van het exemplaar":::

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Open eerst de pagina voor uw Azure Digital Apparaatdubbels-exemplaar in het Azure Portal. Selecteer in het menu van de instantie *toegangs beheer (IAM)*. Selecteer de knop  **+ toevoegen** om een nieuwe roltoewijzing toe te voegen.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecteren om een roltoewijzing toe te voegen op de pagina toegangs beheer (IAM)":::

Vul de waarden in op de volgende pagina *roltoewijzing toevoegen* (moet worden voltooid door een gebruiker met [voldoende machtigingen](#prerequisites-permission-requirements) in het Azure-abonnement):
* **Rol**: Selecteer de *Azure Digital Apparaatdubbels-gegevens eigenaar* in het vervolg keuzemenu
* **Toegang toewijzen aan**: *gebruikers-, groep-of Service-Principal* gebruiken
* **Selecteer**: Zoek de naam of het e-mail adres van de gebruiker die u wilt toewijzen. Wanneer u het resultaat selecteert, wordt de gebruiker weer gegeven in de sectie *geselecteerde leden* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="De vermelde velden invullen in het dialoog venster functie toewijzing toevoegen":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Wanneer u klaar bent met het invoeren van de details, klikt u op de knop *Opslaan* .

### <a name="verify-success"></a>Controleren geslaagd

U kunt de roltoewijzing weer geven die u hebt ingesteld onder *toegangs beheer (IAM) > roltoewijzingen*. De gebruiker moet in de lijst worden weer gegeven met een rol van de eigenaar van de *Azure Digital apparaatdubbels-gegevens*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Weer gave van de roltoewijzingen voor een Azure Digital Apparaatdubbels-exemplaar in Azure Portal":::

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, en u hebt machtigingen toegewezen om het te beheren.

## <a name="next-steps"></a>Volgende stappen

Test afzonderlijke REST API-aanroepen voor uw exemplaar met behulp van de Azure Digital Apparaatdubbels CLI-opdrachten: 
* [AZ DT-referentie](/cli/azure/ext/azure-iot/dt)
* [*Instructies: De Azure Digital Twins-CLI gebruiken*](how-to-use-cli.md)

U kunt ook zien hoe u een client toepassing verbindt met uw exemplaar met verificatie code:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)