---
title: SAML-token versleuteling in Azure Active Directory
description: Meer informatie over het configureren van Azure Active Directory SAML-token versleuteling.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5329f33cc4cbaa3309450165e657fc829c828b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709494"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Procedure: Azure AD SAML-token versleuteling configureren

> [!NOTE]
> Token versleuteling is een functie van Azure Active Directory (Azure AD) Premium. Zie [prijzen voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over Azure AD-edities,-functies en-prijzen.

SAML-token versleuteling maakt het gebruik mogelijk van versleutelde SAML-bevestigingen met een toepassing die deze ondersteunt. Wanneer de configuratie voor een toepassing is geconfigureerd, versleutelt Azure AD de SAML-beweringen die worden verzonden voor die toepassing met behulp van de open bare sleutel die is verkregen van een certificaat dat is opgeslagen in azure AD. De toepassing moet de bijbehorende persoonlijke sleutel gebruiken om het token te ontsleutelen voordat het kan worden gebruikt als bewijs van authenticatie voor de aangemelde gebruiker.

Het versleutelen van de SAML-bevestigingen tussen Azure AD en de toepassing biedt extra zekerheid dat de inhoud van het token niet kan worden onderschept en dat persoonlijke of zakelijke gegevens zijn aangetast.

Zelfs zonder token versleuteling worden Azure AD SAML-tokens nooit door gegeven in de heldere. Azure AD vereist dat de uitwisseling van token aanvragen of-antwoorden plaatsvindt via versleutelde HTTPS/TLS-kanalen, zodat de communicatie tussen de IDP, browser en toepassing plaatsvindt via versleutelde koppelingen. Houd rekening met de waarde van token encryptie voor uw situatie vergeleken met de overhead van het beheer van extra certificaten.   

Als u token versleuteling wilt configureren, moet u een X. 509-certificaat bestand uploaden dat de open bare sleutel bevat naar het Azure AD-toepassings object dat de toepassing vertegenwoordigt. Als u het X. 509-certificaat wilt ophalen, kunt u het downloaden van de toepassing zelf of het verkrijgen van de leverancier van de toepassing in gevallen waarin de leverancier van de toepassing versleutelings sleutels levert of in gevallen waarin de toepassing een persoonlijke sleutel verwacht, kan deze worden gemaakt met behulp van crypto grafie-hulpprogram ma's, het persoonlijke sleutel gedeelte dat is geüpload naar Azure AD.

Azure AD maakt gebruik van AES-256 voor het versleutelen van de gegevens van de SAML-verklaring.

## <a name="configure-saml-token-encryption"></a>Configureren van SAML-tokenversleuteling

Voer de volgende stappen uit om SAML-token versleuteling te configureren:

1. Verkrijg een certificaat met een open bare sleutel dat overeenkomt met een persoonlijke sleutel die in de toepassing is geconfigureerd.

    Maak een asymmetrisch sleutel paar om te gebruiken voor versleuteling. Als de toepassing een open bare sleutel voor versleuteling levert, volgt u de instructies van de toepassing om het X. 509-certificaat te downloaden.

    De open bare sleutel moet worden opgeslagen in een X. 509-certificaat bestand in. CER-indeling.

    Als de toepassing gebruikmaakt van een sleutel die u voor uw exemplaar maakt, volgt u de instructies van uw toepassing voor het installeren van de persoonlijke sleutel die de toepassing gebruikt om tokens te ontsleutelen vanuit uw Azure AD-Tenant.

1. Voeg het certificaat toe aan de toepassings configuratie in azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Token versleuteling configureren in de Azure Portal

U kunt het open bare certificaat toevoegen aan de configuratie van uw toepassing binnen het Azure Portal.

1. Ga naar de [Azure Portal](https://portal.azure.com).

1. Ga naar de Blade **Azure Active Directory > zakelijke toepassingen** en selecteer vervolgens de toepassing waarvoor u de token versleuteling wilt configureren.

1. Selecteer op de pagina van de toepassing **token versleuteling**.

    ![Optie voor token versleuteling in de Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > De optie **token Encryption** is alleen beschikbaar voor SAML-toepassingen die zijn ingesteld op de Blade **bedrijfs toepassingen** in de Azure Portal, hetzij vanuit de toepassings galerie of een niet-galerie-app. Voor andere toepassingen is deze menu optie uitgeschakeld. Voor toepassingen die zijn geregistreerd via de **app-registraties** -ervaring in de Azure Portal kunt u versleuteling configureren voor SAML-tokens met behulp van het toepassings manifest, via Microsoft Graph of via Power shell.

1. Selecteer op de pagina **token versleuteling** de optie **certificaat importeren** om het CER-bestand te importeren dat uw open bare X. 509-certificaat bevat.

    ![Importeer het CER-bestand dat het X. 509-certificaat bevat](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Zodra het certificaat is geïmporteerd en de persoonlijke sleutel is geconfigureerd voor gebruik aan de kant van de toepassing, kunt u versleuteling activeren door de **...** naast de vingerafdruk status te selecteren en vervolgens **token versleuteling activeren** te selecteren in de opties in de vervolg keuzelijst.

1. Selecteer **Ja** om de activering van het certificaat voor token versleuteling te bevestigen.

1. Controleer of de SAML-beweringen die voor de toepassing zijn verzonden, zijn versleuteld.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Token versleuteling in de Azure Portal deactiveren

1. Ga in het Azure Portal naar **Azure Active Directory > zakelijke toepassingen** en selecteer vervolgens de toepassing waarvoor SAML-token versleuteling is ingeschakeld.

1. Selecteer op de pagina van de toepassing **token versleuteling**, zoek het certificaat en selecteer vervolgens de optie **...** om het vervolg keuzemenu weer te geven.

1. Selecteer **token versleuteling deactiveren**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-token versleuteling configureren met behulp van Graph API, Power shell of app-manifest

Versleutelings certificaten worden opgeslagen op het toepassings object in azure AD met een `encrypt` gebruiks label. U kunt meerdere versleutelings certificaten configureren en de certificerings instanties die actief zijn voor het versleutelen van tokens, wordt geïdentificeerd door het- `tokenEncryptionKeyID` kenmerk.

U hebt de object-ID van de toepassing nodig voor het configureren van token versleuteling met behulp van Microsoft Graph-API of Power shell. U kunt deze waarde programmatisch vinden, of door naar de **Eigenschappen** pagina van de toepassing in de Azure portal te gaan en de **object-id-** waarde te gebruiken.

Wanneer u een sleutel referentie configureert met Graph, Power shell of in het manifest van de toepassing, moet u een GUID genereren om te gebruiken voor de keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Token versleuteling configureren met behulp van Microsoft Graph

1. Werk de toepassing `keyCredentials` bij met een X. 509-certificaat voor versleuteling. In het volgende voor beeld ziet u hoe u dit doet.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identificeer het versleutelings certificaat dat actief is voor het versleutelen van tokens. In het volgende voor beeld ziet u hoe u dit doet.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Token versleuteling configureren met Power shell

1. Gebruik de nieuwste Azure AD Power shell-module om verbinding te maken met uw Tenant.

1. Stel de coderings instellingen voor tokens in met de opdracht **[set-azurekunt](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)** .

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Lees de versleutelings instellingen voor tokens met behulp van de volgende opdrachten.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Token versleuteling configureren met het toepassings manifest

1. Ga vanuit het Azure Portal naar **Azure Active Directory > app-registraties**.

1. Selecteer **alle apps** in de vervolg keuzelijst om alle apps weer te geven en selecteer vervolgens de bedrijfs toepassing die u wilt configureren.

1. Op de pagina van de toepassing selecteert u **manifest** om het [toepassings manifest](../develop/reference-app-manifest.md)te bewerken.

1. Stel de waarde voor het `tokenEncryptionKeyId` kenmerk in.

    In het volgende voor beeld ziet u een manifest van een toepassing dat is geconfigureerd met twee versleutelings certificaten en met de tweede die als actieve is geselecteerd met behulp van de tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Volgende stappen

* Ontdek [hoe Azure ad het SAML-protocol gebruikt](../develop/active-directory-saml-protocol-reference.md)
* Meer informatie over de indeling, beveiligings kenmerken en de inhoud van [SAML-tokens in azure AD](../develop/reference-saml-tokens.md)