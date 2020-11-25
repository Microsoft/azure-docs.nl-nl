---
title: Een huis stijl toevoegen aan de aanmeldings pagina van uw organisatie-Azure AD
description: Instructies voor het toevoegen van de huis stijl van uw organisatie op de aanmeldings pagina van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb138ade0f579a7545c0910646b6adfb7d5ac02a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996658"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Een huis stijl toevoegen aan de Azure Active Directory aanmeldings pagina van uw organisatie
Gebruik het logo van uw organisatie en aangepaste kleuren schema's om een consistent uiterlijk te geven op uw aanmeldings pagina's van Azure Active Directory (Azure AD). Uw aanmeldings pagina's worden weer gegeven wanneer gebruikers zich aanmelden bij de webtoepassingen van uw organisatie, zoals Microsoft 365, die Azure AD als id-provider gebruikt.

>[!NOTE]
>Voor het toevoegen van aangepaste huis stijl hebt u Azure Active Directory Premium 1 of Premium 2-licenties nodig. Zie voor meer informatie over licenties en edities [Aanmelden registreren voor Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium-edities zijn beschikbaar voor klanten in China met behulp van de wereld wijde instantie van Azure Active Directory. Azure AD Premium-edities worden momenteel niet ondersteund in de Azure-service die wordt beheerd door 21Vianet in China. Neem contact met ons op via het [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/) voor meer informatie.

## <a name="customize-your-azure-ad-sign-in-page"></a>Uw aanmeldings pagina voor Azure AD aanpassen
U kunt uw aanmeldings pagina's van Azure AD aanpassen, die worden weer gegeven wanneer gebruikers zich aanmelden bij de Tenant-specifieke apps van uw organisatie, zoals `https://outlook.com/contoso.com` , of bij het door geven van een domein variabele, zoals `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

Uw aangepaste huis stijl wordt niet onmiddellijk weer gegeven wanneer uw gebruikers naar sites gaan, zoals www- \. Office.com. In plaats daarvan moet de gebruiker zich aanmelden voordat uw aangepaste huis stijl wordt weer gegeven. Nadat de gebruiker zich heeft aangemeld, kan het 15 minuten of langer duren voordat de huis stijl wordt weer gegeven. 

> [!NOTE]
> Alle huisstijl elementen zijn optioneel. Als u bijvoorbeeld een banner logo zonder achtergrond afbeelding opgeeft, wordt in de aanmeldings pagina uw logo weer gegeven met een standaard achtergrond afbeelding van de doel site (bijvoorbeeld Microsoft 365).<br><br>Daarnaast worden persoonlijke micro soft-accounts niet door de huis stijl van de aanmeldings pagina getransporteerd. Als uw gebruikers of zakelijke gasten zich aanmelden met een persoonlijke Microsoft-account, komt de aanmeldings pagina niet overeen met de huis stijl van uw organisatie.

### <a name="to-customize-your-branding"></a>Uw huis stijl aanpassen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer vervolgens **bedrijfs huisstijl** en selecteer vervolgens **configureren**.

    ![De pagina huis stijl contoso: de optie gemarkeerd configureren](media/customize-branding/company-branding-configure-button.png)

3. Geef op de pagina **huis stijl van bedrijf configureren** een of meer van de volgende informatie op.

    >[!IMPORTANT]
    >Alle aangepaste installatie kopieën die u op deze pagina toevoegt, hebben afbeeldings grootte (pixels) en mogelijke bestands grootte (KB), beperkingen. Als gevolg van deze beperkingen moet u waarschijnlijk een foto-editor gebruiken om de juiste installatie kopieën te maken.

    - **Algemene instellingen**

        ![De pagina bedrijfs huisstijl configureren, met de algemene instellingen voltooid](media/customize-branding/configure-company-branding-general-settings.png)

        - **Japanse.** De taal wordt automatisch ingesteld als standaard en kan niet worden gewijzigd.
        
        - **Achtergrond afbeelding van de aanmeldings pagina.** Selecteer een PNG-of jpg-afbeeldings bestand dat wordt weer gegeven als achtergrond voor uw aanmeldings pagina's. De afbeelding wordt verankerd naar het midden van de browser en wordt geschaald naar de grootte van de zicht bare ruimte. U kunt geen afbeelding selecteren die groter is dan 1920 pixels of die een bestands grootte hebben van meer dan 300 KB.
        
            Het is raadzaam installatie kopieën te gebruiken zonder een sterke focus van een onderwerp, bijvoorbeeld een ondoorzichtig wit vak wordt weer gegeven in het midden van het scherm, en kan elk deel van de afbeelding bedekken, afhankelijk van de afmetingen van de zicht bare ruimte.

        - **Banner logo.** Selecteer een PNG-of JPG-versie van uw logo om op de aanmeldings pagina te worden weer gegeven nadat de gebruiker een gebruikers naam heeft ingevoerd en op de portal pagina **mijn apps** .
            
            De afbeelding mag niet groter zijn dan 60 pixels of breder zijn dan 280 pixels. We raden u aan een transparante installatie kopie te gebruiken omdat de achtergrond mogelijk niet overeenkomt met de achtergrond van uw logo. We raden u ook aan om opvulling rond de afbeelding niet toe te voegen of uw logo lijkt klein te maken.

        - **Hint voor gebruikers naam.** Typ de tekst van de hint die wordt weer gegeven voor gebruikers als ze hun gebruikers naam verg eten. Deze tekst moet Unicode zijn, zonder links of code, en mag niet langer zijn dan 64 tekens. Als gasten zich aanmelden bij uw app, raden we u aan deze Hint niet toe te voegen.

        - **Tekst en opmaak van de aanmeldings pagina.** Typ de tekst die wordt weer gegeven aan de onderkant van de aanmeldings pagina. U kunt deze tekst gebruiken om aanvullende informatie te communiceren, zoals het telefoon nummer van uw Help Desk of een juridische verklaring. Deze tekst moet Unicode zijn en mag niet langer zijn dan 1024 tekens.

           U kunt de tekst van de aanmeldings pagina aanpassen die u hebt ingevoerd. Als u een nieuwe alinea wilt beginnen, gebruikt u de Enter-toets twee keer. U kunt ook de tekst opmaak wijzigen zodat deze vet, cursief, een onderstreping of klik bare koppeling bevat. Gebruik de volgende syntaxis om opmaak toe te voegen aan tekst: 

          > Invoegen ```[text](link)``` 
          
          > Vet: ``` **text** ``` of ``` __text__ ``` 
          
          > Cursief: ``` *text* ``` of ``` _text_ ``` 
          
          > Underline ``` ++text++ ``` 

    - **Geavanceerde instellingen**
            
        ![De pagina bedrijfs huisstijl configureren, met geavanceerde instellingen voltooid](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Achtergrond kleur van de aanmeldings pagina.** Geef de hexadecimale kleur op (bijvoorbeeld wit is #FFFFFF) die in plaats van de achtergrond afbeelding wordt weer gegeven in de verbindings situatie met een lage band breedte. We raden u aan om de primaire kleur van uw banner logo of uw organisatie kleur te gebruiken.

        - **Vier Kante logo afbeelding.** Selecteer een. PNG-afbeelding (voor keur) of. jpg van het logo van uw organisatie om aan gebruikers te zien tijdens het installatie proces voor nieuwe Windows 10 Enter prise-apparaten. Deze installatie kopie wordt alleen gebruikt voor Windows-verificatie en wordt alleen weer gegeven op tenants die gebruikmaken van [Windows auto pilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) voor implementatie of voor pagina's met wachtwoord invoer in andere Windows 10-ervaringen. In sommige gevallen kan het ook worden weer gegeven in het dialoog venster voor toestemming.
        
            De afbeelding mag niet groter zijn dan de grootte van de 240x240 pixels en moet een bestands grootte hebben van minder dan 10 KB. We raden u aan een transparante installatie kopie te gebruiken omdat de achtergrond mogelijk niet overeenkomt met de achtergrond van uw logo. We raden u ook aan om opvulling rond de afbeelding niet toe te voegen of uw logo lijkt klein te maken.
    
        - **Vier Kante logo afbeelding, donker thema.** Hetzelfde als de bovenstaande logo afbeelding hierboven. Deze logo afbeelding neemt de plaats van de afbeelding met het vier Kante logo bij gebruik met een donkere achtergrond, zoals bij Windows 10 Azure AD-schermen toegevoegd tijdens de out-of-Box Experience (OOBE).  Als uw logo goed wordt weer gegeven op een witte, donkere blauwe en zwarte achtergrond, hoeft u deze afbeelding niet toe te voegen. 
        
        - **De optie weer geven om aangemeld te blijven.** U kunt ervoor kiezen uw gebruikers aangemeld te blijven bij Azure AD voordat ze zich expliciet afmelden. Als u **Nee** kiest, wordt deze optie verborgen en moeten gebruikers zich aanmelden telkens wanneer de browser wordt gesloten en opnieuw wordt geopend.

            Deze mogelijkheid is alleen beschikbaar voor het standaard huisstijl object en niet voor een specifiek taal object. Zie [de prompt ' aangemeld blijven? ' configureren voor Azure AD-accounts](keep-me-signed-in.md) voor meer informatie over het configureren en oplossen van problemen met de optie om aangemeld te blijven.
        
            >[!NOTE]
            >Of sommige functies van SharePoint Online en Office 2010 beschikbaar zijn, hangt ervan af of gebruikers ervoor kunnen kiezen aangemeld te blijven. Als u deze instelling op **Nee** instelt, krijgen uw gebruikers mogelijk extra en onverwachte prompts te zien om zich aan te melden.
   

3. Wanneer u klaar bent met het toevoegen van uw huis stijl, selecteert u **Opslaan**.

    Als dit proces uw eerste aangepaste huisstijl configuratie maakt, wordt dit de standaard instelling voor uw Tenant. Als u aanvullende configuraties hebt, kunt u uw standaard configuratie kiezen.
    
    >[!IMPORTANT]
    >Als u meer bedrijfs huisstijl configuraties wilt toevoegen aan uw Tenant, moet u **nieuwe taal** kiezen op de pagina **huis stijl van Contoso-bedrijf** . Hiermee opent u de pagina **bedrijfs huisstijl configureren** , waar u dezelfde stappen kunt volgen als hierboven.

## <a name="update-your-custom-branding"></a>Uw aangepaste huis stijl bijwerken
Nadat u uw aangepaste huis stijl hebt gemaakt, kunt u teruggaan en alles wat u wilt wijzigen.

### <a name="to-edit-your-custom-branding"></a>Uw aangepaste huis stijl bewerken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer vervolgens **bedrijfs huisstijl** en selecteer vervolgens **configureren**.

    ![Pagina huis stijl contoso, met de standaard configuratie weer gegeven](media/customize-branding/company-branding-default-config.png)

3. Op de pagina **huis stijl van bedrijf configureren kunt u** informatie toevoegen, verwijderen of wijzigen, op basis van de beschrijvingen in de sectie [uw Azure AD-aanmeldings pagina aanpassen](#customize-your-azure-ad-sign-in-page) van dit artikel.

4. Selecteer **Opslaan**.

   Het kan een uur duren voordat de wijzigingen die u aan de huisstijl van de aanmeldingspagina hebt aangebracht, worden weergegeven.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Taalspecifieke huisstijl toevoegen aan uw directory
U kunt de taal van de oorspronkelijke configuratie niet wijzigen in de standaard taal. Als u echter een configuratie in een andere taal nodig hebt, kunt u een nieuwe configuratie maken.

### <a name="to-add-a-language-specific-branding-configuration"></a>Een taalspecifieke huisstijl configuratie toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer vervolgens **bedrijfs huisstijl** en selecteer vervolgens **nieuwe taal**.

    ![Contoso-bedrijfs huisstijl pagina, met de optie nieuwe taal gemarkeerd](media/customize-branding/company-branding-new-language.png)

3. Selecteer op de pagina **huis stijl van bedrijf configureren** uw taal (bijvoorbeeld Frans) en voeg vervolgens de vertaalde gegevens toe, op basis van de beschrijvingen in het gedeelte [uw Azure AD-aanmeldings pagina aanpassen](#customize-your-azure-ad-sign-in-page) van dit artikel.

4. Selecteer **Opslaan**.

    De **huismerk pagina van contoso:** Hiermee wordt de nieuwe Franse configuratie weer gegeven.

    ![De pagina huis stijl contoso: de nieuwe taal configuratie die wordt weer gegeven](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Uw aangepaste huis stijl toevoegen aan pagina's
Voeg uw aangepaste huis stijl aan pagina's toe door het einde van de URL te wijzigen in de tekst `?whr=yourdomainname` . Deze aanpassing werkt op verschillende pagina's, met inbegrip van de installatie pagina voor de Multi-Factor Authentication (MFA), de Setup-pagina voor het opnieuw instellen van wacht woorden (SSPR) en de aanmeldings pagina.

**Voorbeelden:**

**Oorspronkelijke URL:**https://aka.ms/MFASetup<br>
**Aangepaste URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Oorspronkelijke URL:**https://aka.ms/SSPR<br>
**Aangepaste URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
