---
title: Azure Active Directory integratie voor Azure Red Hat open Shift
description: Meer informatie over het maken van een Azure AD-beveiligings groep en-gebruiker voor het testen van apps op uw Microsoft Azure Red Hat open Shift-cluster.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633322"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory integratie voor Azure Red Hat open Shift

> [!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt 30 juni 2022 buiten gebruik gesteld. Ondersteuning voor het maken van nieuwe Azure Red Hat open Shift 3,11-clusters loopt voort tot en met 30 november 2020. Na de buiten gebruiks telling worden de resterende Azure Red Hat open Shift 3,11-clusters afgesloten om beveiligings problemen te voor komen.
> 
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:arofeedback@microsoft.com)opnemen.

Als u nog geen Azure Active Directory-Tenant (Azure AD) hebt gemaakt, volgt u de instructies in [een Azure AD-Tenant maken voor Azure Red Hat open Shift](howto-create-tenant.md) voordat u doorgaat met deze instructies.

Microsoft Azure Red Hat open Shift moet machtigingen hebben om namens uw cluster taken uit te voeren. Als uw organisatie nog geen Azure AD-gebruiker, Azure AD-beveiligings groep of Azure AD-App-registratie heeft om te gebruiken als Service-Principal, volgt u deze instructies om ze te maken.

## <a name="create-a-new-azure-active-directory-user"></a>Een nieuwe Azure Active Directory-gebruiker maken

Zorg er in de [Azure Portal](https://portal.azure.com)voor dat uw Tenant wordt weer gegeven onder uw gebruikers naam in de rechter bovenhoek van de portal:

![Scherm afbeelding van de portal met Tenant rechtsboven ](./media/howto-create-tenant/tenant-callout.png) als de verkeerde Tenant wordt weer gegeven, klikt u in de rechter bovenhoek op uw gebruikers naam, klikt u op **Active Directory** en selecteert u de juiste Tenant in de lijst **alle directory's** .

Maak een nieuwe Azure Active Directory eigenaar van de gebruiker om u aan te melden bij uw Azure Red Hat open Shift-cluster.

1. Ga naar de Blade [gebruikers-alle gebruikers](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Klik op **+ nieuwe gebruiker** om het deel venster **gebruiker** te openen.
3. Voer een **naam** in voor deze gebruiker.
4. Maak een **gebruikers naam** op basis van de naam van de Tenant die u hebt gemaakt, die  `.onmicrosoft.com` aan het einde is toegevoegd. Bijvoorbeeld `yourUserName@yourTenantName.onmicrosoft.com`. Noteer deze gebruikers naam. U hebt deze nodig om u aan te melden bij uw cluster.
5. Klik op **Directory functie** om het deel venster Directory-rollen te openen, selecteer **eigenaar** en klik onder aan het deel venster op **OK** .
6. Klik in het deel venster **gebruiker** op **wacht woord weer geven** en het tijdelijke wacht woord vastleggen. Nadat u zich de eerste keer hebt aangemeld, wordt u gevraagd om deze opnieuw in te stellen.
7. Klik onder aan het deel venster op **maken** om de gebruiker te maken.

## <a name="create-an-azure-ad-security-group"></a>Een Azure AD-beveiligingsgroep maken

Als u de toegang tot cluster beheer wilt verlenen, worden de lidmaatschappen in een Azure AD-beveiligings groep gesynchroniseerd met de open Shift-groep OSA-Customer-Administrators. Als u niets opgeeft, wordt er geen cluster beheer toegang verleend.

1. Open de Blade [Azure Active Directory groepen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Klik op **+ nieuwe groep**.
3. Geef een groeps naam en beschrijving op.
4. Stel het **groeps type** in op **beveiliging**.
5. Stel het **lidmaatschaps type** in op **toegewezen**.

    Voeg de Azure AD-gebruiker die u in de vorige stap hebt gemaakt, toe aan deze beveiligings groep.

6. Klik op **leden** om het deel venster **leden selecteren** te openen.
7. Selecteer in de lijst leden de Azure AD-gebruiker die u hierboven hebt gemaakt.
8. Klik onder aan de portal op **selecteren** en vervolgens op **maken** om de beveiligings groep te maken.

    Noteer de waarde van de groeps-ID.

9. Wanneer de groep is gemaakt, wordt deze weer geven in de lijst met alle groepen. Klik op de nieuwe groep.
10. Op de pagina die wordt weer gegeven, kopieert u de **object-id**. We verwijzen naar deze waarde, zoals `GROUPID` in de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) .

> [!IMPORTANT]
> Als u deze groep wilt synchroniseren met de OSA-klant-admins open Shift-groep, maakt u het cluster met behulp van de Azure CLI. De Azure Portal heeft momenteel geen veld om deze groep in te stellen.

## <a name="create-an-azure-ad-app-registration"></a>Een Azure AD-App-registratie maken

U kunt automatisch een app-registratie-client voor Azure Active Directory (Azure AD) maken als onderdeel van het maken van het cluster door de `--aad-client-app-id` vlag te weglaten aan de `az openshift create` opdracht. In deze zelf studie leert u hoe u de Azure AD-App-registratie voor volledigheid maakt.

Als uw organisatie nog geen app-registratie voor Azure Active Directory (Azure AD) heeft om als service-principal te gebruiken, volgt u deze instructies om er een te maken.

1. Open de [blade app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) en klik op **+ nieuwe registratie**.
2. Voer in het deel venster **een toepassing registreren** een naam in voor de registratie van uw toepassing.
3. Zorg ervoor dat onder de **ondersteunde account typen** **alleen accounts in deze organisatie Directory** zijn geselecteerd. Dit is de veiligste keuze.
4. We gaan later een omleidings-URI toevoegen wanneer we de URI van het cluster kennen. Klik op de knop registreren om de registratie **van** de Azure AD-toepassing te maken.
5. Op de pagina die wordt weer gegeven, kopieert u de **toepassings-id (client)**. We verwijzen naar deze waarde, zoals `APPID` in de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) .

![Scherm afbeelding van de pagina met app-objecten](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Een clientgeheim maken

Genereer een client geheim om uw app te verifiëren voor Azure Active Directory.

1. Klik in het gedeelte **beheren** van de pagina app-registraties op **certificaten & geheimen**.
2. Klik in het deel venster **certificaten & geheimen** op **+ Nieuw client geheim**.  Het deel venster **een geheim van client toevoegen** wordt weer gegeven.
3. Geef een **Beschrijving** op.
4. Stel **verloopt** op de gewenste duur, bijvoorbeeld **in 2 jaar**.
5. Klik op **toevoegen** en de sleutel waarde wordt weer gegeven in de sectie **client geheimen** van de pagina.
6. Kopieer de sleutel waarde. We verwijzen naar deze waarde, zoals `SECRET` in de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) .

![Scherm afbeelding van het deel venster certificaten en geheimen](./media/howto-create-tenant/create-key.png)

Zie voor meer informatie over Azure-toepassing-objecten [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Zie [een app registreren bij het Azure Active Directory v 1.0-eind punt](../active-directory/develop/quickstart-register-app.md)voor meer informatie over het maken van een nieuwe Azure AD-toepassing.

## <a name="add-api-permissions"></a>API-machtigingen toevoegen

[//]: # (Wijzig de Microsoft Graph niet. Het werkt niet met Microsoft Graph.)
1. Klik in de sectie **beheren** op **API-machtigingen**
2. Klik op **machtiging toevoegen** en selecteer **Azure Active Directory Graph** vervolgens **gedelegeerde machtigingen**.
> [!NOTE]
> Zorg ervoor dat u het Azure Active Directory Graph hebt geselecteerd en niet de tegel ' Microsoft Graph '.

3. Vouw de **gebruiker** uit op de onderstaande lijst en schakel de machtiging **gebruiker. Read** in. Als **User. Read** standaard is ingeschakeld, moet u ervoor zorgen dat het de **Azure Active Directory Graph** permission **gebruiker. Read**.
4. Schuif omhoog en selecteer **toepassings machtigingen**.
5. Vouw **map** in de onderstaande lijst uit en Schakel **Directory. ReadAll** in.
6. Klik op **machtigingen toevoegen** om de wijzigingen te accepteren.
7. In het paneel API-machtigingen moeten nu zowel *User. Read* als *Directory. ReadAll* worden weer gegeven. Let op de waarschuwing in de **beheerders vergunning vereist** kolom naast *Directory. ReadAll*.
8. Als u de beheerder van het *Azure-abonnement* bent, klikt u op **toestemming van beheerder verlenen voor *abonnements naam*** hieronder. Als u niet de beheerder van het *Azure-abonnement* bent, vraagt u de toestemming van de beheerder aan.

![Scherm opname van het paneel API-machtigingen. Gebruiker. Read en Directory. ReadAll machtigingen toegevoegd, toestemming van de beheerder vereist voor Directory. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> De synchronisatie van de groep cluster beheerders werkt pas nadat de toestemming is verleend. U ziet een groene cirkel met een vinkje en een bericht ' verleend voor *abonnements naam*' in de kolom *vereiste beheerder* .

Zie [Azure-abonnements beheerders toevoegen of wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md)voor meer informatie over het beheren van beheerders en andere rollen.

## <a name="resources"></a>Resources

* [Toepassingen en Service-Principal-objecten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)
* [Snelstart: Een app registreren bij het Azure Active Directory v1.0-eindpunt](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Volgende stappen

Als u aan alle [Azure Red Hat open Shift-vereisten](howto-setup-environment.md)hebt voldaan, kunt u uw eerste cluster maken.

Probeer de zelf studie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)