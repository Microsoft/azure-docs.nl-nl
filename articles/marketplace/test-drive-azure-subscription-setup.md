---
title: Een Azure Marketplace-abonnement instellen voor gehoste test stations
description: In dit artikel wordt uitgelegd hoe u een Azure Marketplace-abonnement instelt voor Dynamics 365 voor klant betrokkenheid en Dynamics 365 voor Operations-test stations
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: a7f12891bf394e54ee46c60598536faed1731202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600875"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Een Azure Marketplace-abonnement instellen voor gehoste test stations

In dit artikel wordt uitgelegd hoe u een Azure Marketplace-abonnement en **dynamics 365** kunt instellen voor de klant betrokkenheid of **Dynamics 365 voor de bewerkings** omgeving van een test station.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Instellen voor Dynamics 365 voor klant betrokkenheid

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een beheerders account.
2. Controleer of u zich in de Tenant bevindt die is gekoppeld aan uw Dynamics 365 test drive-exemplaar door in de rechter bovenhoek op het pictogram van uw account te bewegen. Als u zich niet in de juiste Tenant bevindt, selecteert u het account pictogram om over te scha kelen naar de juiste Tenant.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="De juiste Tenant te selecteren.":::

3. Controleer of de **Dynamics 365** -licentie voor het Customer engagement-abonnement beschikbaar is.

    [![De plan licentie controleren.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Een Azure Active Directory (AD)-app maken in Azure. AppSource maakt gebruik van deze app voor het inrichten en ongedaan maken van de inrichting van de test drive-gebruiker in uw Tenant.
    1. Selecteer **Azure Active Directory** in het deel venster filter.
    2. Selecteer **App-registraties**.

        [![App-registraties selecteren.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Selecteer **Nieuwe registratie**.
    4. Geef de naam van de juiste toepassing op.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Een toepassing registreren.":::

    5. Onder ondersteunde account typen selecteert u **account in een organisatie Directory en persoonlijke micro soft-accounts**.
    6. Selecteer **maken** en wacht tot uw app is gemaakt.
    7. Nadat de app is gemaakt, noteert u de **toepassings-id** die wordt weer gegeven op het scherm overzicht. U hebt deze waarde later nodig bij het configureren van uw test drive.
    8. Onder **toepassing beheren** selecteert u **API-machtigingen**.
    9. Selecteer **een machtiging toevoegen** en vervolgens **Microsoft Graph-API**.
    10. Selecteer de categorie **toepassings** machtiging en vervolgens de **gebruikers. readwrite. all**, **map. Read. all** en **Directory. readwrite. all** machtigingen.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="De toepassings machtigingen instellen.":::

    11. Zodra de machtiging is toegevoegd, selecteert u **toestemming van beheerder verlenen voor micro soft**.
    12. Selecteer **Ja** in het bericht waarschuwing.

        [![Hier worden de toepassings machtigingen weer gegeven die zijn verleend.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Een geheim genereren voor de Azure AD-app:
        1. Selecteer bij **toepassing beheren** de optie **certificaat en geheimen**.
        2. Onder client geheimen selecteert u **Nieuw client geheim**.
        3. Voer een beschrijving in, zoals *test station*, en selecteer een geschikte duur. De test drive wordt gebreekt zodra deze sleutel is verlopen, op welk moment moet u een nieuwe sleutel genereren en AppSource geven.
        4. Selecteer **toevoegen** om het Azure app-geheim te genereren. Kopieer deze waarde wanneer deze wordt verborgen zodra u deze Blade lave. U hebt deze waarde later nodig bij het configureren van uw test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Een client geheim toevoegen.":::

5. Voeg de functie van de Service-Principal toe aan de toepassing zodat de Azure AD-app gebruikers uit uw Azure-Tenant kan verwijderen.
    1. Open een Power shell-opdracht prompt op beheer niveau.
    2. Install-Module MSOnline (Voer deze opdracht uit als MSOnline niet is geïnstalleerd).
    3. Connect-MsolService (er wordt een pop-upvenster weer gegeven; Meld u aan met de zojuist gemaakte organisatie-Tenant).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Meld u aan bij uw account.":::

6. Voeg de hierboven gemaakte Azure-app als een toepassings gebruiker toe aan uw test drive CRM-exemplaar.
    1. Voeg een nieuwe gebruiker toe in **Azure Active Directory**. Alleen **naam** -en **gebruikersnaam** waarden (die horen bij dezelfde Tenant) zijn vereist voor het maken van deze gebruiker. Wijzig de andere velden als standaard. Kopieer de waarde van de gebruikers naam.
    2. Meld u aan bij **CRM-exemplaar** en selecteer   >  **beveiligings**  >  **gebruikers** instellen.
    3. Wijzig de weer gave in **toepassings gebruikers**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Account gegevens voor een gebruiker instellen.":::

    4. Voeg een nieuwe gebruiker toe (zorg ervoor dat het formulier voor de gebruiker van de toepassing is).
    5. Geef dezelfde gebruikers naam op in de velden **primaire e-mail** en **gebruikers naam** . Voeg de **Azure ApplicationId** toe in de **toepassings-id**.
    6. Geef een **volledige naam**.
    7. Selecteer **Opslaan**.
    8. Selecteer **rollen beheren**.
    9. Wijs een aangepaste of OOB-beveiligingsrol toe die machtigingen voor lezen, schrijven en toewijzen bevat, zoals *systeem beheerder*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="De rol privileges selecteren.":::

    10. Schakel ook de actie **handelen namens een andere gebruiker in** .
    11. Wijs de gebruiker van de toepassing de aangepaste beveiligingsrol toe die u hebt gemaakt voor uw test drive.

## <a name="set-up-for-dynamics-365-for-operations"></a>Instellen voor Dynamics 365 voor bewerkingen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een beheerders account.
2. Controleer of u zich in de Tenant bevindt die is gekoppeld aan uw Dynamics 365 test drive-exemplaar door in de rechter bovenhoek op het pictogram van uw account te bewegen. Als u zich niet in de juiste Tenant bevindt, selecteert u het account pictogram om over te scha kelen naar de juiste Tenant.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="De juiste Tenant te selecteren.":::

3. Maak een Azure AD-app in Azure. AppSource maakt gebruik van deze app voor het inrichten en ongedaan maken van de inrichting van de test drive-gebruiker in uw Tenant.
    1. Selecteer **Azure Active Directory** in het deel venster filter.
    2. Selecteer **App-registraties**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Een app-registratie selecteren.":::

    3. Selecteer **Nieuwe registratie**.
    4. Geef de naam van de juiste toepassing op.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Een toepassing registreren.":::

    5. Onder ondersteunde account typen selecteert u **account in een organisatie Directory en persoonlijke micro soft-accounts**.
    6. Selecteer **maken** en wacht tot uw app is gemaakt.
    7. Nadat de app is gemaakt, noteert u de **toepassings-id** die wordt weer gegeven op het scherm overzicht. U hebt deze waarde later nodig bij het configureren van uw test drive.
    8. Onder **toepassing beheren** selecteert u **API-machtigingen**.
    9. Selecteer **een machtiging toevoegen** en vervolgens **Microsoft Graph-API**.
    10. Selecteer de categorie **toepassings** machtiging en vervolgens de **Directory. Read. all** -en **Directory. readwrite. all** -machtigingen.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Toepassings machtigingen instellen.":::

    11. Selecteer **machtiging toevoegen**.
    12. Zodra de machtiging is toegevoegd, selecteert u **toestemming van beheerder verlenen voor micro soft**.
    13. Selecteer **Ja** in het bericht waarschuwing.

        [![Toont de toepassings machtigingen die zijn verleend.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Een geheim genereren voor de Azure AD-app:
        1. Selecteer bij **toepassing beheren** de optie **certificaat en geheimen**.
        2. Onder client geheimen selecteert u **Nieuw client geheim**.
        3. Voer een beschrijving in, zoals *test station*, en selecteer een geschikte duur. De test drive wordt gebreekt zodra deze sleutel is verlopen, op welk moment moet u een nieuwe sleutel genereren en AppSource geven.
        4. Selecteer **toevoegen** om het Azure app-geheim te genereren. Kopieer deze waarde wanneer deze wordt verborgen zodra u deze Blade lave. U hebt deze waarde later nodig bij het configureren van uw test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Een client geheim toevoegen.":::

4. Voeg de functie van de Service-Principal toe aan de toepassing zodat de Azure AD-app gebruikers uit uw Azure-Tenant kan verwijderen.
    1. Open een Power shell-opdracht prompt op beheer niveau.
    2. Install-Module MSOnline (Voer deze opdracht uit als MSOnline niet is geïnstalleerd).
    3. Connect-MsolService (er wordt een pop-upvenster weer gegeven; Meld u aan met de zojuist gemaakte organisatie-Tenant).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Meld u aan bij uw account.":::

5. Voeg nu de bovenstaande app toe aan **Dynamics 365 voor bewerkingen** om de app in staat te stellen gebruikers te beheren.
    1. Zoek uw **Dynamics 365 voor bewerkingen** -exemplaar.
    2. Klik in de linkerbovenhoek op het menu met drie regels (Hamburger).
    3. Selecteer **systeem beheer**.
    4. Selecteer **Azure Active Directory toepassingen**.
    5. Selecteer **+ Nieuw**.
    6. Voer de **client-id in van de Azure AD-App** die namens de acties moet uitvoeren.

    > [!NOTE]
    > De gebruikers-ID waarvoor de acties worden uitgevoerd (doorgaans de systeem beheerder van het exemplaar of een gebruiker die bevoegdheden heeft om andere gebruikers toe te voegen).

    [![De gebruikers-ID waarvoor de acties worden uitgevoerd (doorgaans de systeem beheerder van het exemplaar of een gebruiker die bevoegdheden heeft om andere gebruikers toe te voegen).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
