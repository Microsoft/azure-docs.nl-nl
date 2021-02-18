---
title: Functie claim voor Azure AD-apps voor bedrijven configureren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van de rol claim die is uitgegeven in het SAML-token voor zakelijke toepassingen in Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: aab1f99984ed5286692cbf9dae39fb4f7d28599c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652459"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Procedure: de rol claim configureren die is uitgegeven in het SAML-token voor zakelijke toepassingen

Door Azure Active Directory (Azure AD) te gebruiken, kunt u het claim type voor de rol claim aanpassen in het antwoord token dat u ontvangt nadat u een app hebt geautoriseerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure AD-abonnement met Directory-installatie.
- Een abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld. U moet SSO configureren met uw toepassing.

> [!NOTE]
> In dit artikel wordt uitgelegd hoe u toepassings rollen maakt/bijwerkt of verwijdert op de service-principal met behulp van Api's in azure AD. Als u de nieuwe gebruikers interface voor app-rollen wilt gebruiken, raadpleegt u de Details [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

## <a name="when-to-use-this-feature"></a>Wanneer u deze functie gebruikt

Gebruik deze functie als uw toepassing aangepaste rollen verwacht in het SAML-antwoord dat door Azure AD wordt geretourneerd. U kunt zoveel rollen maken als u wilt.

## <a name="create-roles-for-an-application"></a>Rollen maken voor een toepassing

1. In de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>, selecteert u in het linkerdeelvenster het pictogram **Azure Active Directory**.

    ![Azure Active Directory pictogram][1]

2. Selecteer **Enterprise-toepassingen**. Selecteer vervolgens **alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen][2]

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven in het dialoog venster.

    ![Knop nieuwe toepassing][3]

4. Typ in het zoekvak de naam van uw toepassing en selecteer vervolgens uw toepassing in het deel venster voor resultaten. Selecteer de knop **toevoegen** om de toepassing toe te voegen.

    ![Toepassing in de lijst met resultaten](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Nadat de toepassing is toegevoegd, gaat u naar de pagina **Eigenschappen** en kopieert u de object-id.

    ![Eigenschappen pagina](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster en voer de volgende stappen uit:

    1. Meld u aan bij de Graph Explorer-site met behulp van de referenties van de globale beheerder of cobeheerder voor uw Tenant.

    1. U hebt voldoende machtigingen om de rollen te maken. Selecteer **machtigingen voor wijzigen** om de machtigingen op te halen.

        ![De knop machtigingen wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > De rol van de Cloud app-beheerder en de app-beheerder werken niet in dit scenario omdat de globale beheerders machtigingen voor de Directory lezen en schrijven nodig zijn.

    1. Selecteer de volgende machtigingen in de lijst (als u dit nog niet hebt) en selecteer **machtigingen wijzigen**.

        ![Lijst met machtigingen en de knop machtigingen wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Accepteer de toestemming. U bent weer aangemeld bij het systeem.

    1. Wijzig de versie in **beta** en haal de lijst met Service-principals op uit de Tenant met behulp van de volgende query:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Als u meerdere mappen gebruikt, volgt u dit patroon: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Graph Explorer in het dialoog venster met de query voor het ophalen van service-principals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. Ga in de lijst met opgehaalde service-principals naar het account dat u wilt wijzigen. U kunt ook CTRL + F gebruiken om de toepassing te doorzoeken vanuit alle vermelde service-principals. Zoek de object-ID die u hebt gekopieerd op de pagina **Eigenschappen** en gebruik de volgende query om naar de service-principal te gaan:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![Query voor het ophalen van de service-principal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Extraheer de eigenschap **appRoles** uit het object Service-Principal.

        ![Details van de eigenschap appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Als u de aangepaste app gebruikt (niet de Azure Marketplace-app), ziet u twee standaard rollen: gebruiker en msiam_access. Voor de Marketplace-app is msiam_access de enige standaard rol. U hoeft geen wijzigingen aan te brengen in de standaard rollen.

    1. Nieuwe rollen genereren voor uw toepassing.

        De volgende JSON is een voor beeld van het object **appRoles** . Maak een vergelijkbaar object om de functies toe te voegen die u wilt toevoegen aan uw toepassing.

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        U kunt alleen nieuwe rollen toevoegen na msiam_access voor de patch-bewerking. U kunt ook zoveel rollen toevoegen als uw organisatie nodig heeft. Azure AD verzendt de waarde van deze rollen als de claim waarde in het SAML-antwoord. Als u de GUID-waarden voor de ID van nieuwe rollen wilt genereren, gebruikt u de [volgende](https://www.guidgenerator.com/) webtoepassingen

    1. Ga terug naar Graph Explorer en wijzig de methode van **Get** to **patch**. Patch het Service-Principal-object voor de gewenste rollen door de eigenschap **appRoles** bij te werken zoals in het vorige voor beeld. Selecteer **query uitvoeren** om de patch bewerking uit te voeren. Bij een geslaagd bericht wordt bevestigd dat de rol is gemaakt.

        ![Patch bewerking met geslaagd bericht](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. Wanneer de Service-Principal is bijgewerkt met meer rollen, kunt u gebruikers toewijzen aan de respectieve rollen. U kunt de gebruikers toewijzen door naar de portal te gaan en te bladeren naar de toepassing. Selecteer het tabblad **gebruikers en groepen** . Op dit tabblad wordt een lijst weer gegeven met alle gebruikers en groepen die al zijn toegewezen aan de app. U kunt nieuwe gebruikers toevoegen aan de nieuwe rollen. U kunt ook een bestaande gebruiker selecteren en **bewerken** selecteren om de rol te wijzigen.

    ![Tabblad gebruikers en groepen](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Als u de rol aan een wille keurige gebruiker wilt toewijzen, selecteert u de nieuwe rol en selecteert u de knop **toewijzen** onder aan de pagina.

    ![Deel venster toewijzing bewerken en rollen selecteren](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Vernieuw uw sessie in de Azure Portal om nieuwe rollen weer te geven.

1. Werk de **kenmerken** tabel bij om een aangepaste toewijzing van de rol claim te definiëren.

1. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Kenmerknaam | Kenmerk waarde |
    | -------------- | ----------------|
    | Rolnaam  | user.assignedroles |

    Als de waarde van de rol claim null is, zal Azure AD deze waarde niet in het token verzenden en dit is standaard per ontwerp.

    1. Klik op pictogram **bewerken** om **gebruikers kenmerken** te openen & dialoog venster claims.

        ![Scherm opname van het bewerkings pictogram waarmee het dialoog venster gebruikers kenmerken & claims wordt geopend.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. Voeg in het dialoog venster **gebruikers claims beheren** het SAML-token kenmerk toe door te klikken op **nieuwe claim toevoegen**.

        ![Knop kenmerk toevoegen](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        ![Het deel venster kenmerk toevoegen](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. Typ in het vak **naam** de naam van het kenmerk, indien nodig. In dit voor beeld wordt de **rolnaam** gebruikt als claim naam.

    1. Laat het vak **Naamruimte** leeg.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Selecteer **Opslaan**.

10. Als u uw toepassing wilt testen in eenmalige aanmelding die wordt geïnitieerd door een id-provider, meldt u zich aan bij het [toegangs venster](https://myapps.microsoft.com) en selecteert u de tegel van de toepassing. In het SAML-token ziet u alle toegewezen rollen voor de gebruiker met de claim naam die u hebt opgegeven.

## <a name="update-an-existing-role"></a>Een bestaande rol bijwerken

Voer de volgende stappen uit om een bestaande rol bij te werken:

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Meld u aan bij de Graph Explorer-site met behulp van de referenties van de globale beheerder of cobeheerder voor uw Tenant.

1. Wijzig de versie in **beta** en haal de lijst met Service-principals op uit de Tenant met behulp van de volgende query:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, volgt u dit patroon: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer in het dialoog venster met de query voor het ophalen van service-principals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Ga in de lijst met opgehaalde service-principals naar het account dat u wilt wijzigen. U kunt ook CTRL + F gebruiken om de toepassing te doorzoeken vanuit alle vermelde service-principals. Zoek de object-ID die u hebt gekopieerd op de pagina **Eigenschappen** en gebruik de volgende query om naar de service-principal te gaan:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query voor het ophalen van de service-principal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Extraheer de eigenschap **appRoles** uit het object Service-Principal.

    ![Details van de eigenschap appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. Gebruik de volgende stappen om de bestaande functie bij te werken.

    ![De aanvraag tekst voor ' PATCH ' met ' description ' en ' DisplayName ' gemarkeerd](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Wijzig de methode van **Get** to **patch**.

    1. Kopieer de bestaande rollen en plak deze onder de **hoofd tekst** van de aanvraag.

    1. Werk de waarde van een rol bij door de beschrijving, de functie waarde of de weergave naam van de rol zo nodig bij te werken.

    1. Nadat u alle vereiste rollen hebt bijgewerkt, selecteert u **query uitvoeren**.

## <a name="delete-an-existing-role"></a>Een bestaande rol verwijderen

Als u een bestaande functie wilt verwijderen, voert u de volgende stappen uit:

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster.

1. Meld u aan bij de Graph Explorer-site met behulp van de referenties van de globale beheerder of cobeheerder voor uw Tenant.

1. Wijzig de versie in **beta** en haal de lijst met Service-principals op uit de Tenant met behulp van de volgende query:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, volgt u dit patroon: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer in het dialoog venster met de query voor het ophalen van de lijst met Service-principals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Ga in de lijst met opgehaalde service-principals naar het account dat u wilt wijzigen. U kunt ook CTRL + F gebruiken om de toepassing te doorzoeken vanuit alle vermelde service-principals. Zoek de object-ID die u hebt gekopieerd op de pagina **Eigenschappen** en gebruik de volgende query om naar de service-principal te gaan:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query voor het ophalen van de service-principal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Extraheer de eigenschap **appRoles** uit het object Service-Principal.

    ![Details van de eigenschap appRoles van het Service-Principal-object](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. Als u de bestaande functie wilt verwijderen, gebruikt u de volgende stappen.

    ![Hoofd tekst van de aanvraag voor ' PATCH ' waarbij IsEnabled is ingesteld op False](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Wijzig de methode van **Get** to **patch**.

    1. Kopieer de bestaande rollen van de toepassing en plak deze onder de **hoofd tekst** van de aanvraag.

    1. Stel de waarde **IsEnabled** in op **False** voor de functie die u wilt verwijderen.

    1. Selecteer **Query uitvoeren**.

    Zorg ervoor dat u de rol msiam_access hebt en dat de ID overeenkomt met de gegenereerde rol.

1. Nadat de rol is uitgeschakeld, verwijdert u het Role-blok uit de sectie **appRoles** . Behoud de methode als **patch** en selecteer **query uitvoeren**.

1. Nadat u de query hebt uitgevoerd, wordt de rol verwijderd.

    De rol moet worden uitgeschakeld voordat deze kan worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de [app-documentatie](../saas-apps/tutorial-list.md)voor extra stappen.

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
