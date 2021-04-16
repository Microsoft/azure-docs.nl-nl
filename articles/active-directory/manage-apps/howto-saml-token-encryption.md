---
title: SAML-tokenversleuteling in Azure Active Directory
description: Meer informatie over het configureren Azure Active Directory SAML-tokenversleuteling.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: iangithinji
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c06a499cccb03e6726ee19542d7eb79e0c99b43
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375726"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>How to: Configure Azure AD SAML token encryption (Azure AD SAML-tokenversleuteling configureren)

> [!NOTE]
> Tokenversleuteling is een Azure Active Directory (Azure AD) Premium-functie. Zie Prijzen voor Azure AD voor meer informatie over edities, functies en [prijzen van Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

Met SAML-tokenversleuteling kunt u versleutelde SAML-assertie gebruiken met een toepassing die dit ondersteunt. Wanneer azure AD is geconfigureerd voor een toepassing, worden de SAML-asserten die worden verzonden voor die toepassing versleuteld met behulp van de openbare sleutel die is verkregen van een certificaat dat is opgeslagen in Azure AD. De toepassing moet de overeenkomende persoonlijke sleutel gebruiken om het token te ontsleutelen voordat het kan worden gebruikt als bewijs van verificatie voor de aangemelde gebruiker.

Het versleutelen van de SAML-asserties tussen Azure AD en de toepassing biedt extra zekerheid dat de inhoud van het token niet kan worden onderschept en dat persoonlijke of zakelijke gegevens niet kunnen worden onderschept.

Zelfs zonder tokenversleuteling worden Azure AD SAML-tokens nooit helemaal zonder tokenversleuteling doorgegeven aan het netwerk. Azure AD vereist uitwisseling van tokenaanvraag/-antwoord via versleutelde HTTPS/TLS-kanalen, zodat communicatie tussen de IDP, browser en toepassing via versleutelde koppelingen wordt uitgevoerd. Houd rekening met de waarde van tokenversleuteling voor uw situatie vergeleken met de overhead van het beheren van extra certificaten.   

Als u tokenversleuteling wilt configureren, moet u een X.509-certificaatbestand met de openbare sleutel uploaden naar het Azure AD-toepassingsobject dat de toepassing vertegenwoordigt. Als u het X.509-certificaat wilt verkrijgen, kunt u het downloaden van de toepassing zelf of het ophalen bij de leverancier van de toepassing in gevallen waarin de leverancier van de toepassing versleutelingssleutels verstrekt of in gevallen waarin de toepassing verwacht dat u een persoonlijke sleutel op te geven, deze kan worden gemaakt met cryptografieprogramma's, het gedeelte van de persoonlijke sleutel dat is geüpload naar het sleutelopslag van de toepassing en het overeenkomende openbare-sleutelcertificaat dat is geüpload naar Azure AD.

Azure AD gebruikt AES-256 om de SAML-assertygegevens te versleutelen.

## <a name="configure-saml-token-encryption"></a>Configureren van SAML-tokenversleuteling

Volg deze stappen om SAML-tokenversleuteling te configureren:

1. Verkrijg een certificaat met een openbare sleutel dat overeenkomt met een persoonlijke sleutel die in de toepassing is geconfigureerd.

    Maak een asymmetrisch sleutelpaar voor versleuteling. Als de toepassing een openbare sleutel levert voor versleuteling, volgt u de instructies van de toepassing om het X.509-certificaat te downloaden.

    De openbare sleutel moet worden opgeslagen in een X.509-certificaatbestand in CER-indeling.

    Als de toepassing een sleutel gebruikt die u voor uw exemplaar maakt, volgt u de instructies van uw toepassing voor het installeren van de persoonlijke sleutel die de toepassing gebruikt om tokens van uw Azure AD-tenant te ontsleutelen.

1. Voeg het certificaat toe aan de toepassingsconfiguratie in Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Tokenversleuteling configureren in de Azure Portal

U kunt het openbare certificaat toevoegen aan uw toepassingsconfiguratie in de Azure Portal.

1. Ga naar de [Azure Portal](https://portal.azure.com).

1. Ga naar de **blade Azure Active Directory > Bedrijfstoepassingen** en selecteer vervolgens de toepassing voor wie u tokenversleuteling wilt configureren.

1. Selecteer tokenversleuteling op de **pagina van de toepassing.**

    ![De optie Tokenversleuteling in de Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > De **optie Tokenversleuteling** is alleen beschikbaar voor SAML-toepassingen die zijn ingesteld via de blade **Bedrijfstoepassingen** in de Azure Portal, vanuit de toepassingsgalerie of een niet-galerie-app. Voor andere toepassingen is deze menuoptie uitgeschakeld. Voor toepassingen die zijn geregistreerd **via App-registraties-ervaring** in de Azure Portal, kunt u versleuteling voor SAML-tokens configureren met behulp van het toepassingsmanifest, via Microsoft Graph of via PowerShell.

1. Selecteer certificaat importeren op de pagina **Tokenversleuteling** **om** het CER-bestand met uw openbare X.509-certificaat te importeren.

    ![Importeer het CER-bestand dat het X.509-certificaat bevat](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Zodra het certificaat is geïmporteerd en de persoonlijke sleutel is geconfigureerd voor gebruik aan de toepassingszijde, activeert u versleuteling door **...** naast de vingerafdrukstatus te selecteren en selecteert u vervolgens **Tokenversleuteling** activeren in de opties in de vervolgkeuzelijst.

1. Selecteer **Ja om** de activering van het tokenversleutelingscertificaat te bevestigen.

1. Controleer of de SAML-asserten die voor de toepassing worden verzonden, zijn versleuteld.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Tokenversleuteling in de Azure Portal

1. Ga in Azure Portal naar **Azure Active Directory > Bedrijfstoepassingen** en selecteer vervolgens de toepassing waarin SAML-tokenversleuteling is ingeschakeld.

1. Selecteer op de pagina van de toepassing **Tokenversleuteling,** zoek het certificaat en selecteer vervolgens de optie **...** om de vervolgkeuzelijst weer te geven.

1. Selecteer **Tokenversleuteling deactiveren.**

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-tokenversleuteling configureren met Graph API, PowerShell of app-manifest

Versleutelingscertificaten worden opgeslagen op het toepassingsobject in Azure AD met een `encrypt` gebruikstag. U kunt meerdere versleutelingscertificaten configureren en de certificaten die actief zijn voor het versleutelen van tokens worden geïdentificeerd door het `tokenEncryptionKeyID` kenmerk .

U hebt de object-id van de toepassing nodig om tokenversleuteling te configureren met behulp Microsoft Graph API of PowerShell. U kunt deze waarde programmatisch vinden of door  naar de pagina Eigenschappen van de toepassing te gaan in de Azure Portal de **waarde object-id op te** geven.

Wanneer u een keyCredential configureert met behulp van Graph, PowerShell of in het toepassingsmanifest, moet u een GUID genereren voor gebruik voor de keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Tokenversleuteling configureren met Microsoft Graph

1. Werk de van de toepassing `keyCredentials` bij met een X.509-certificaat voor versleuteling. In het volgende voorbeeld ziet u hoe u dit doet.

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

1. Identificeer het versleutelingscertificaat dat actief is voor het versleutelen van tokens. In het volgende voorbeeld ziet u hoe u dit doet.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Tokenversleuteling configureren met Behulp van PowerShell

1. Gebruik de nieuwste Azure AD PowerShell-module om verbinding te maken met uw tenant.

1. Stel de instellingen voor tokenversleuteling in **[met de opdracht Set-AzureApplication.](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Lees de instellingen voor tokenversleuteling met behulp van de volgende opdrachten.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Tokenversleuteling configureren met behulp van het toepassingsmanifest

1. Ga vanuit Azure Portal naar **Azure Active Directory > App-registraties**.

1. Selecteer **Alle apps** in de vervolgkeuze selecteren om alle apps weer te geven en selecteer vervolgens de bedrijfstoepassing die u wilt configureren.

1. Selecteer manifest op de pagina van de toepassing **om** het toepassingsmanifest [te bewerken.](../develop/reference-app-manifest.md)

1. Stel de waarde voor het kenmerk `tokenEncryptionKeyId` in.

    In het volgende voorbeeld ziet u een toepassingsmanifest dat is geconfigureerd met twee versleutelingscertificaten en met de tweede als actief manifest met behulp van het tokenEnryptionKeyId.

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

* Ontdek hoe [Azure AD gebruikmaakt van het SAML-protocol](../develop/active-directory-saml-protocol-reference.md)
* Meer informatie over de indeling, beveiligingskenmerken en inhoud van [SAML-tokens in Azure AD](../develop/reference-saml-tokens.md)