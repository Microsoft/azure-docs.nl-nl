---
title: Op rollen gebaseerd toegangs beheer van Azure gebruiken voor StorSimple | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt in de context van StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518973"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Toegangs beheer op basis van rollen in azure voor StorSimple

In dit artikel vindt u een korte beschrijving van de manier waarop Azure RBAC (op rollen gebaseerd toegangs beheer) voor uw StorSimple-apparaat kan worden gebruikt. Azure RBAC biedt verfijnd toegangs beheer voor Azure. Gebruik Azure RBAC om alleen de juiste hoeveelheid toegang tot de StorSimple-gebruikers toe te kennen om hun taken uit te voeren in plaats van iedereen onbeperkte toegang te geven. Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie over de basis principes van toegangs beheer in Azure.

Dit artikel is van toepassing op apparaten met de StorSimple 8000-serie waarop update 3,0 of hoger wordt uitgevoerd in de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Azure-rollen voor StorSimple

Er kan Azure RBAC worden toegewezen op basis van de rollen. De rollen zorgen voor bepaalde machtigings niveaus op basis van de beschik bare resources in de omgeving. Er zijn twee soorten rollen waaruit StorSimple-gebruikers kunnen kiezen: ingebouwd of aangepast.

* **Ingebouwde rollen** : de ingebouwde rollen kunnen eigenaar, bijdrager, lezer of beheerder voor gebruikers toegang zijn. Zie [ingebouwde rollen voor op rollen gebaseerde Access Control voor Azure](../role-based-access-control/built-in-roles.md)voor meer informatie.

* **Aangepaste rollen** : als de ingebouwde rollen niet aan uw behoeften voldoen, kunt u aangepaste Azure-rollen maken voor StorSimple. Als u een aangepaste Azure-rol wilt maken, begint u met een ingebouwde rol, bewerkt u deze en importeert u deze vervolgens weer in de omgeving. Het downloaden en uploaden van de rol wordt beheerd met behulp van Azure PowerShell of de Azure CLI. Zie [aangepaste rollen maken voor op rollen gebaseerd Access Control](../role-based-access-control/custom-roles.md)voor meer informatie.

Als u de verschillende beschik bare rollen voor een StorSimple apparaat gebruiker in het Azure Portal wilt weer geven, gaat u naar uw StorSimple Apparaatbeheer-service en gaat u naar **toegangs beheer (IAM) > rollen**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Een aangepaste rol maken voor de beheerder van de StorSimple-infra structuur

In het volgende voor beeld beginnen we met de ingebouwde functie **lezer** waarmee gebruikers alle resource bereiken kunnen bekijken, maar niet kunnen bewerken of nieuwe maken. Vervolgens wordt deze rol uitgebreid om een nieuwe aangepaste rol StorSimple infrastructuur beheerder te maken. Deze rol wordt toegewezen aan gebruikers die de infra structuur voor de StorSimple-apparaten kunnen beheren.

1. Voer Windows Power shell uit als beheerder.

2. Aanmelden bij Azure.

    `Connect-AzAccount`

3. Exporteer de rol van lezer als een JSON-sjabloon op uw computer.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Open het JSON-bestand in Visual Studio. U ziet dat een typische Azure-rol bestaat uit drie hoofd secties, **acties**, **intacte** en **AssignableScopes**.

    In de sectie **actie** worden alle toegestane bewerkingen voor deze rol weer gegeven. Elke actie wordt toegewezen van een resource provider. Voor een StorSimple-infrastructuur beheerder gebruikt u de `Microsoft.StorSimple` resource provider.

    Gebruik Power shell om alle resource providers weer te geven die beschikbaar en geregistreerd zijn in uw abonnement.

    `Get-AzResourceProvider`

    U kunt ook controleren op alle beschik bare Power shell-cmdlets om de resource providers te beheren.

    In de secties **intact** worden alle beperkte acties voor een bepaalde Azure-rol weer gegeven. In dit voor beeld zijn geen acties beperkt.
    
    Onder de **AssignableScopes** worden de abonnements-id's weer gegeven. Zorg ervoor dat de rol Azure de expliciete abonnements-ID bevat waarin deze wordt gebruikt. Als de juiste abonnements-ID niet is opgegeven, is het niet toegestaan om de rol in uw abonnement te importeren.

    Bewerk het bestand om de voor gaande overwegingen te onthouden.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importeer de aangepaste Azure-rol terug naar de omgeving.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Deze rol zou nu moeten worden weer gegeven in de lijst met rollen op de Blade **toegangs beheer** .

![Azure-rollen weer geven](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Ga voor meer informatie naar [aangepaste rollen](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Voorbeeld uitvoer voor het maken van aangepaste rollen via de Power shell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Gebruikers toevoegen aan de aangepaste rol

U verleent toegang vanuit de resource en resourcegroep die of het abonnement dat het bereik van de roltoewijzing is. Wanneer u toegang verleent, moet u er rekening mee houdt dat de toegang die is verleend op het bovenliggende knoop punt wordt overgenomen door de onderliggende. Ga voor meer informatie naar [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md).

1. Ga naar **toegangs beheer (IAM)**. Klik op **+ toevoegen** op de Blade toegangs beheer.

    ![Toegang tot Azure-rol toevoegen](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecteer de rol die u wilt toewijzen. in dit geval is het de beheerder van de **StorSimple-infra structuur**.

3. Selecteer de gebruiker, groep of toepassing in de directory aan wie/waaraan u toegang wilt verlenen. U kunt zoeken in de directory met weergavenamen, e-mailadressen en object-id's.

4. Selecteer **Opslaan** om de toewijzing te maken.

    ![Machtigingen toevoegen aan Azure-rol](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Met een melding bij het toevoegen van een **gebruiker** wordt de voortgang bijgehouden. Nadat de gebruiker is toegevoegd, wordt de lijst met gebruikers in Access Control bijgewerkt.

## <a name="view-permissions-for-the-custom-role"></a>Machtigingen voor de aangepaste rol weer geven

Zodra deze rol is gemaakt, kunt u de machtigingen weer geven die zijn gekoppeld aan deze rol in de Azure Portal.

1. Als u de machtigingen wilt weer geven die aan deze rol zijn gekoppeld, gaat u naar **toegangs beheer (IAM) > rollen > StorSimple infrastructuur beheerder**. De lijst met gebruikers in deze rol wordt weer gegeven.

2. Selecteer een gebruiker van de StorSimple-infrastructuur beheerder en klik op **machtigingen**.

    ![Machtigingen voor de beheerdersrol van StorSimple-infra structuur weer geven](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. De machtigingen die aan deze rol zijn gekoppeld, worden weer gegeven.

    ![Gebruikers weer geven in StorSimple infra admin-rol](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toewijzen van aangepaste rollen voor interne en externe gebruikers](../role-based-access-control/role-assignments-external-users.md).
