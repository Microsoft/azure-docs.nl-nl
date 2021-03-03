---
title: Facebook toevoegen als een id-provider-Azure AD
description: Met Facebook communiceren om externe gebruikers (gasten) in te scha kelen voor aanmelding bij uw Azure AD-apps met hun eigen Facebook-accounts.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68f83bd042af6612b91807f2adeed54d24bfe01
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648612"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook toevoegen als een id-provider voor externe identiteiten

U kunt Facebook toevoegen aan uw Self-service voor het registreren van gebruikers, zodat gebruikers zich met hun eigen Facebook-accounts kunnen aanmelden bij uw toepassingen. Als u gebruikers wilt toestaan zich aan te melden met Facebook, moet u eerst [selfservice registratie inschakelen](self-service-sign-up-user-flow.md) voor uw Tenant. Nadat u Facebook als een id-provider hebt toegevoegd, stelt u een gebruikers stroom voor de toepassing in en selecteert u Facebook als een van de aanmeldings opties.

Nadat u Facebook hebt toegevoegd als een van de aanmeldings opties van uw toepassing, kunt u op de **aanmeldings** pagina gewoon het e-mail adres invoeren waarmee de gebruiker zich aanmeldt bij Facebook, of u kunt **aanmeldings opties** selecteren en **Aanmelden met Facebook** kiezen. In beide gevallen worden ze omgeleid naar de aanmeldings pagina van Facebook voor verificatie.

![Aanmeldings opties voor Facebook-gebruikers](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> Gebruikers kunnen hun Facebook-accounts alleen gebruiken om zich aan te melden via apps met behulp van selfservice registratie en gebruikers stromen. Gebruikers kunnen niet worden uitgenodigd en hun uitnodiging inwisselen met een Facebook-account.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Een app maken in de Facebook-ontwikkelaars console

Als u een Facebook-account wilt gebruiken als een [ID-provider](identity-providers.md), moet u een toepassing maken in de Facebook Developers-console. Als u nog geen Facebook-account hebt, kunt u zich aanmelden bij [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Gebruik de volgende Url's in de stappen 9 en 16 hieronder.
> - Voor de **site-URL** voert u het adres van uw toepassing in, bijvoorbeeld `https://contoso.com` .
> - Voer in voor **geldige OAuth omleidings-uri's** `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . U vindt de `<tenant-ID>` Blade overzicht van Azure Active Directory.


1. Meld u aan bij [Facebook for developers](https://developers.facebook.com/) (Facebook voor ontwikkelaars) met de referenties van uw Facebook-account.
2. Als u dit nog niet hebt gedaan, moet u zich registreren als Facebook-ontwikkelaar. Als u dit wilt doen, selecteert u **Get Started** in de rechter bovenhoek van de pagina, accepteert u het beleid van Facebook en voert u de registratiestappen uit.
3. Selecteer **My Apps** en vervolgens **Create App**.
4. Voer een **weergavenaam** en een geldig **e-mailadres van de contactpersoon** in.
5. Selecteer **App-ID maken**. Hiervoor moet u mogelijk het beleid voor het Facebook-platform accepteren en een onlinebeveiligingscontrole uitvoeren.
6. Selecteer **Settings** > **Basic**.
7. Kies een **categorie**, bijvoorbeeld Business en Pages. Deze waarde is vereist voor Facebook, maar wordt niet gebruikt voor Azure AD.
8. Selecteer onder aan de pagina **Add platform** en selecteer vervolgens **Website**.
9. Voer in **site-URL** de juiste URL in (hierboven aangegeven).
10. Voer in het **URL-privacybeleid** de URL in voor de pagina waar u privacy-informatie voor uw toepassing onderhoudt, bijvoorbeeld `http://www.contoso.com` .
11. Selecteer **Save changes**.
12. Kopieer de waarde van de **App-ID** aan de bovenkant van de pagina.
13. Selecteer **weer geven** en kopieer de waarde van het **app-geheim**. U kunt beide gebruiken om Facebook te configureren als een id-provider in uw Tenant. **App-geheim** is een belang rijke beveiligings referentie.
14. Selecteer het plus teken naast **producten** en selecteer vervolgens **instellen** onder **Facebook-aanmelding**.
15. Selecteer onder **Facebook-aanmelding** de optie **instellingen**.
16. In **geldige OAuth omleidings-uri's** voert u de juiste URL in (hierboven aangegeven).
17. Selecteer **Save changes** onder aan de pagina.
18. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD, selecteert u in de rechter bovenhoek van de pagina de status kiezer en schakelt u deze **in** om de toepassing openbaar te maken en vervolgens **Switch modus** te selecteren. Op dit moment moet de status worden gewijzigd van **ontwikkeling** naar **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Een Facebook-account configureren als een id-provider
Nu gaat u de ID van de Facebook-client en het client geheim instellen, hetzij door het in te voeren in de Azure AD-portal of met behulp van Power shell. U kunt uw Facebook-configuratie testen door u aan te melden via een gebruikers stroom op een app waarvoor Self-Service-aanmelding is ingeschakeld.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Facebook-Federatie configureren in de Azure AD-Portal
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van uw Azure AD-Tenant.
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **alle id-providers** en selecteer vervolgens **Facebook**.
5. Voer voor de **client-id** de **App-ID** in van de Facebook-toepassing die u eerder hebt gemaakt.
6. Voer voor het **client geheim** het **app-geheim** in dat u hebt vastgelegd.

   ![Scherm afbeelding van de pagina Social ID-provider toevoegen](media/facebook-federation/add-social-identity-provider-page.png)

7. Selecteer **Opslaan**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Facebook-Federatie configureren met behulp van Power shell
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer de volgende opdracht uit: `Connect-AzureAD` .
3. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account.  
4. Voer de volgende opdracht uit: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Gebruik de client-ID en het client geheim van de app die u hierboven hebt gemaakt in de Facebook-ontwikkelaars console. Zie het artikel [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) voor meer informatie. 

## <a name="how-do-i-remove-facebook-federation"></a>Hoe kan ik Facebook-Federatie verwijderen?
U kunt de instellingen van uw Facebook-Federatie verwijderen. Als u dit doet, kunnen gebruikers die zich hebben geregistreerd via gebruikers stromen met hun Facebook-accounts, zich niet meer aanmelden. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Facebook-Federatie verwijderen in de Azure AD-portal: 
1. Ga naar de [Azure Portal](https://portal.azure.com). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**.
3. Selecteer **alle id-providers**.
4. Selecteer op de **Facebook** -regel het snelmenu (**...**) en selecteer vervolgens **verwijderen**. 
5. Selecteer **Ja** om het verwijderen te bevestigen.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Facebook-Federatie verwijderen met behulp van Power shell: 
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer `Connect-AzureAD` uit.  
4. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account.  
5. Voer de volgende opdracht in:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Zie [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

- [Een self-service aanmelding toevoegen aan een app](self-service-sign-up-user-flow.md)