---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Jumio
titleSuffix: Azure AD B2C
description: In deze zelf studie configureert u Azure Active Directory B2C met Jumio voor geautomatiseerde ID-verificatie, waarbij klant gegevens worden beschermd.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928661"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van Jumio met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure Active Directory B2C (Azure AD B2C) met [Jumio](https://www.jumio.com/). Jumio is een ID-verificatie service waarmee realtime-verificatie via een automatische ID wordt ingeschakeld om klant gegevens te beveiligen.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

De Jumio-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het controleren van de referenties van de gebruiker. Het wordt ook wel de ID-provider genoemd.

- Jumio: de service die de ID-gegevens gebruikt die door de gebruiker worden verstrekt en deze verifieert.

- Tussenliggende rest API: de API waarmee de integratie tussen Azure AD B2C en de Jumio-service wordt geïmplementeerd.

- Azure Blob-opslag: de service die aangepaste gebruikers interface bestanden levert aan het Azure AD B2C-beleid.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Diagram van de architectuur van een Azure AD B2C-integratie met Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt een pagina om u aan te melden of zich aan te melden om een account te maken. Azure AD B2C worden de gebruikers kenmerken verzameld.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | De middelste laag API verzamelt gebruikers kenmerken en transformeert deze in een indeling die door Jumio API kan worden verbruikt. Vervolgens worden de kenmerken naar Jumio verzonden.
| 4. | Nadat Jumio de gegevens heeft verbruikt en verwerkt, wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | De middelste laag-API verwerkt de informatie en stuurt relevante informatie terug naar Azure AD B2C.
| 6. | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="sign-up-with-jumio"></a>Aanmelden met Jumio

Als u een Jumio-account wilt maken, neemt u contact op met [Jumio](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Azure AD B2C configureren met Jumio

Nadat u een Jumio-account hebt gemaakt, gebruikt u het account om Azure AD B2C te configureren. In de volgende secties wordt het proces sequentieel beschreven.

### <a name="deploy-the-api"></a>De API implementeren

Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) voor een Azure-service. U kunt de code uit Visual Studio publiceren door [deze instructies](/visualstudio/deployment/quickstart-deploy-to-azure)te volgen.

>[!NOTE]
>U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

### <a name="deploy-the-client-certificate"></a>Het client certificaat implementeren

1. Een client certificaat helpt bij het beveiligen van de API-aanroep van Jumio. Een zelfondertekend certificaat maken met behulp van de volgende Power shell-voorbeeld code:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Het certificaat wordt vervolgens geëxporteerd naar de locatie die is opgegeven voor ``{your-local-path}`` .

3. Importeer het certificaat naar Azure App Service door de instructies in [dit artikel](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)te volgen.

### <a name="create-a-signingencryption-key"></a>Een handtekening/versleutelings sleutel maken

Maak een wille keurige teken reeks met een lengte van meer dan 64 tekens die alleen letters en cijfers bevatten.

Bijvoorbeeld: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Gebruik het volgende Power shell-script om de teken reeks te maken:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>De API configureren

U kunt [Toepassings instellingen configureren in azure app service](../app-service/configure-common.md#configure-app-settings). Met deze methode kunt u instellingen veilig configureren zonder deze in een opslag plaats te controleren. U moet de volgende instellingen opgeven voor de rest-API:

| Toepassingsinstellingen | Bron | Opmerkingen |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio-account configuratie |     |
|JumioSettings:AuthPassword | Jumio-account configuratie |     |
|AppSettings: SigningCertThumbprint|Vinger afdruk van het gemaakte zelfondertekende certificaat|  |
|AppSettings: IdTokenSigningKey| De handtekening sleutel is gemaakt met Power shell | |
| AppSettings: IdTokenEncryptionKey |Versleutelings sleutel gemaakt met Power shell
| AppSettings: IdTokenIssuer | De verlener die moet worden gebruikt voor de JWT-token (een GUID-waarde heeft de voor keur) |
| AppSettings: IdTokenAudience  | De doel groep die moet worden gebruikt voor de JWT-token (een GUID-waarde heeft de voor keur) |
|AppSettings: BaseRedirectUrl | Basis-URL van het Azure AD B2C beleid | https://{uw-Tenant naam}. b2clogin. com/{uw-toepassings-id}|
| WEBSITE_LOAD_CERTIFICATES| Vinger afdruk van het gemaakte zelfondertekende certificaat |

### <a name="deploy-the-ui"></a>De gebruikers interface implementeren

1. Stel een [Blob Storage-container in uw opslag account in](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

2. Sla de bestanden van de gebruikers interface op in de map met de [gebruikers interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) in de BLOB-container.

#### <a name="update-ui-files"></a>Gebruikers interface bestanden bijwerken

1. Ga in de bestanden van de gebruikers interface naar de map [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Open elk HTML-bestand.

3. Zoek en vervang door `{your-ui-blob-container-url}` de URL van de BLOB-container.

4. Zoek en vervang door `{your-intermediate-api-url}` de URL van de TUSSENLIGGENDE API app-service.

>[!NOTE]
> Als best practice raden we u aan om een bericht over toestemming toe te voegen op de pagina met kenmerk verzameling. Informeer gebruikers dat de informatie wordt verzonden naar services van derden voor identiteits verificatie.

### <a name="configure-the-azure-ad-b2c-policy"></a>Het Azure AD B2C-beleid configureren

1. Ga naar het [Azure AD B2C-beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) in de map beleid.

2. Volg [dit artikel](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) om het [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)te downloaden.

3. Configureer het beleid voor de Azure AD B2C Tenant.

>[!NOTE]
>Werk het opgegeven beleid bij om te koppelen aan uw specifieke Tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C-Tenant. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en vervolgens:

   a. Selecteer voor **toepassing** de geregistreerde app (het voor beeld is JWT).

   b. Selecteer de **omleidings-URL** voor de **antwoord-URL**.

   c. Selecteer **gebruikers stroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account.

5. De Jumio-service wordt aangeroepen tijdens de stroom nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
