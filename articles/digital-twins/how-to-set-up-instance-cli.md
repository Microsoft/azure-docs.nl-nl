---
title: Een exemplaar en verificatie instellen (CLI)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen met behulp van de CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7108ee956c18fbcc150b56cbcb8ae1c69841dda4
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198571"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Een Azure Digital Apparaatdubbels-exemplaar en-authenticatie (CLI) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van de CLI.
* Als u deze stappen hand matig wilt door lopen met behulp van de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en verificatie instellen (Portal)*](how-to-set-up-instance-portal.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Een Cloud Shell-sessie instellen
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van de Cloud shell opdracht. U moet het volgende opgeven:
* Een resource groep waar het exemplaar wordt geïmplementeerd. Als u nog geen bestaande resource groep hebt, kunt u er nu een maken met de volgende opdracht:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Een regio voor de implementatie. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Een naam voor uw exemplaar. Als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio waarin de opgegeven naam al wordt gebruikt, wordt u gevraagd een andere naam te kiezen.

Gebruik deze waarden in de volgende opdracht om het exemplaar te maken:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Geslaagde pogingen controleren en belang rijke waarden verzamelen

Als het exemplaar is gemaakt, wordt het resultaat in Cloud Shell er ongeveer als volgt uitzien: het uitvoeren van informatie over de resource die u hebt gemaakt.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Opdrachtvenster met het maken van een resource groep en een Azure Digital Apparaatdubbels-exemplaar":::

Noteer de **hostnaam**, **naam** en **ResourceGroup** van het Azure Digital apparaatdubbels-exemplaar van de uitvoer. Dit zijn alle belang rijke waarden die u mogelijk nodig hebt wanneer u met uw Azure Digital Apparaatdubbels-exemplaar blijft werken om verificatie en gerelateerde Azure-resources in te stellen. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

> [!TIP]
> U kunt deze eigenschappen, samen met alle eigenschappen van uw exemplaar, op elk gewenst moment weer geven door uit te voeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Gebruik de volgende opdracht om de rol toe te wijzen (moet worden uitgevoerd door een gebruiker met [voldoende machtigingen](#prerequisites-permission-requirements) in het Azure-abonnement). Voor de opdracht moet u de *User Principal name* op het Azure ad-account door geven aan de gebruiker aan wie de rol moet worden toegewezen. In de meeste gevallen komt dit overeen met het e-mail adres van de gebruiker op het Azure AD-account.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Het resultaat van deze opdracht is een gegenereerde informatie over de roltoewijzing die is gemaakt.

> [!NOTE]
> Als met deze opdracht een fout wordt geretourneerd die aangeeft dat de CLI **geen gebruiker of service-principal kan vinden in Graph data base**:
>
> Wijs in plaats daarvan de rol toe met behulp van de *object-id* van de gebruiker. Dit kan gebeuren voor gebruikers op persoonlijke [micro soft-accounts (msa's)](https://account.microsoft.com/account). 
>
> Gebruik de [pagina Azure portal van Azure Active Directory gebruikers](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) om het gebruikers account te selecteren en de bijbehorende gegevens te openen. De *ObjectID* van de gebruiker kopiëren:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Weer gave van de gebruikers pagina in Azure Portal markeren van de GUID in het veld object-ID" lightbox="media/includes/user-id.png":::
>
> Herhaal vervolgens de opdracht lijst met roltoewijzingen met de *object-id* van de gebruiker voor de `assignee` bovenstaande para meter.

### <a name="verify-success"></a>Controleren geslaagd

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, en u hebt machtigingen toegewezen om het te beheren.

## <a name="next-steps"></a>Volgende stappen

Test afzonderlijke REST API-aanroepen voor uw exemplaar met behulp van de Azure Digital Apparaatdubbels CLI-opdrachten: 
* [AZ DT-referentie](/cli/azure/ext/azure-iot/dt)
* [*Instructies: De Azure Digital Twins-CLI gebruiken*](how-to-use-cli.md)

U kunt ook zien hoe u een client toepassing verbindt met uw exemplaar met verificatie code:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)