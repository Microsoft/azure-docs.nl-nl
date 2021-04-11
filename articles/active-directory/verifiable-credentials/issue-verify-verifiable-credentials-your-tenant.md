---
title: 'Zelf studie: een probleem en controleer geverifieerde referenties met behulp van uw Azure-Tenant (preview-versie)'
description: Het voor beeld van een verifieer bare referentie code wijzigen om samen te werken met uw Azure-Tenant
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: e4772b6701065a44416d849faa9a501bd7895f27
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553376"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Zelf studie: een probleem en controleer verifieer bare referenties met behulp van uw Tenant (preview-versie)

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Nu u uw Azure-Tenant hebt ingesteld met de Controleer bare referentie service, worden de stappen door lopen die nodig zijn om uw Azure Active Directory (Azure AD) in te scha kelen om verifieer bare referenties te geven met behulp van de voor beeld-app.

In dit artikel kunt u:

> [!div class="checklist"]
> * De voor beeld-app registreren in uw Azure AD
> * Een regel maken en een bestand weer geven
> * Regels uploaden en bestanden weer geven
> * Uw verifieer bare referenties voor de aanmeldings gegevens service instellen om Azure Key Vault te gebruiken
> * Update voorbeeld code met de informatie van uw Tenant.

Voor de voorbeeld code moeten gebruikers worden geverifieerd bij een id-provider, met name Azure AD B2C, voordat de gecontroleerde referentie-expert VC kan worden uitgegeven. Niet alle verifieer bare referentie verleners vereisen authenticatie voordat referenties worden verleend.

Met verificatie-ID-tokens kunnen gebruikers bewijzen dat ze zijn voordat ze hun referenties ontvangen. Wanneer gebruikers zich kunnen aanmelden, retourneert de ID-provider een beveiligings token met claims over de gebruiker. De Issuer-service transformeert deze beveiligings tokens en de bijbehorende claims naar een verifieer bare referentie. De verifieer bare referentie is ondertekend met de verleners.

Een id-provider die ondersteuning biedt voor het OpenID Connect Connect-protocol wordt ondersteund. Voor beelden van ondersteunde identiteits providers zijn [Azure Active Directory](../fundamentals/active-directory-whatis.md)en [Azure AD B2C](../../active-directory-b2c/overview.md). In deze zelf studie gebruiken we AAD.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u de stappen in de [vorige zelf studie](enable-your-tenant-verifiable-credentials.md) al hebt uitgevoerd en dat u toegang hebt tot de omgeving die u hebt gebruikt.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Een app registreren om de portefeuilles in te scha kelen voor het aanmelden van gebruikers

Als u een verifieer bare referentie wilt uitgeven, moet u een app registreren, zodat de verificatie van gebruikers wordt toegestaan, of een andere verifieer bare referentie portefeuille.  

Registreer een toepassing met de naam ' VC Wallet app ' in azure AD en haal een client-ID op.

1. Volg de instructies voor het registreren van een toepassing met [Azure AD](../develop/quickstart-register-app.md) bij het registreren. Gebruik hiervoor de onderstaande waarden.

   - Naam: "VC Wallet-app"
   - Ondersteunde account typen: accounts in deze organisatie-Directory alleen
   - Omleidings-URI: vcclient://openid/

   ![een toepassing registreren](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Nadat u de toepassing hebt geregistreerd, noteert u de ID van de toepassing (client). U hebt deze waarde later nog nodig.

   ![client-ID van toepassing](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Selecteer de knop met de **eind punten** en kopieer de OpenID Connect Connect meta gegevens document-URI. U hebt deze informatie nodig voor de volgende sectie. 

   ![eind punten van de verlener](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>De node-app instellen met toegang tot Azure Key Vault

Voor de verificatie van de aanvraag voor de uitgifte van referenties van een gebruiker worden uw cryptografische sleutels gebruikt in Azure Key Vault. Voor toegang tot Azure Key Vault heeft uw website een client-ID en client geheim nodig dat kan worden gebruikt voor verificatie bij Azure Key Vault.

1. Selecteer bij het bekijken van de overzichts pagina van de VC Wallet-app **certificaten & geheimen**.
    ![certificaten en geheimen](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Kies in de sectie **client geheimen** de optie **Nieuw client geheim**
    1. Een beschrijving toevoegen zoals ' knoop punt VC-client geheim '
    1. Expires: in een jaar.
  ![Toepassings geheim met een verval datum van één jaar](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Kopieer het geheim. U hebt deze informatie nodig om uw voor beeld-node-app bij te werken.

>[!WARNING]
> U hebt één kans om het geheim te kopiëren. Het geheim is op één manier gehasht. Kopieer de ID niet. 

Nadat u de toepassing en het client geheim hebt gemaakt in azure AD, moet u de toepassing de benodigde machtigingen verlenen voor het uitvoeren van bewerkingen op uw Key Vault. Deze machtigings wijzigingen zijn vereist om de website in staat te stellen de persoonlijke sleutels die daar zijn opgeslagen, te openen en te gebruiken.

1. Ga naar Key Vault.
2. Selecteer de sleutel kluis die wordt gebruikt voor deze zelf studies.
3. Kies **toegangs beleid** in het navigatie links
4. Kies **+ toegangs beleid toevoegen**.
5. Kies in de sectie **sleutel machtigingen** **ophalen** en **ondertekenen**.
6. Selecteer **Principal** en gebruik de toepassings-id om te zoeken naar de toepassing die u eerder hebt geregistreerd. Selecteer deze.
7. Selecteer **Toevoegen**.
8. Kies **Opslaan**.

Lees de [sleutel kluis RBAC-hand leiding](../../key-vault/general/rbac-guide.md) voor meer informatie over Key Vault machtigingen en toegangs beheer.

![sleutel kluis machtigingen toewijzen](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Wijzigingen aanbrengen in overeenstemming met uw omgeving

Tot nu toe hebben we met onze voor beeld-app gewerkt. De app maakt gebruik van [Azure Active Directory B2C](../../active-directory-b2c/overview.md) en er wordt nu overgeschakeld naar het gebruik van Azure AD, dus we moeten wijzigingen aanbrengen die niet alleen overeenkomen met uw omgeving, maar ook ondersteuning bieden voor aanvullende claims die niet eerder zijn gebruikt.

1. Kopieer het onderstaande regel bestand en sla het op in **modified-expertRules.jsop**. 

    > [!NOTE]
    > **"scope": "OpenID Connect profile"** is opgenomen in dit regel bestand en is niet opgenomen in het regel bestand van de voor beeld-app. In de volgende sectie wordt uitgelegd hoe u de optionele claims in uw Azure Active Directory-Tenant inschakelt. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Open het bestand en vervang de **client_id** en **configuratie** waarden door de twee waarden die we in de vorige sectie hebben gekopieerd.

    ![de twee waarden markeren die moeten worden gewijzigd als onderdeel van deze stap](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  De waarde **configuratie** is de OpenID Connect Connect meta data document-URI.

  Omdat de voorbeeld code gebruikmaakt van Azure Active Directory B2C en er Azure Active Directory worden gebruikt, moeten we optionele claims via scopes toevoegen, zodat deze claims worden opgenomen in het ID-token dat naar de verifieer bare referentie moet worden geschreven. 

3. Open Azure Active Directory terug in de Azure Portal.
4. Kies **app-registraties**.
5. Open de VC Wallet-app die u eerder hebt gemaakt.
6. Kies **token configuratie**.
7. Selecteer **+ optionele claim toevoegen**

     ![Voeg onder token configuratie een optionele claim toe](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Kies in het **token type** **id** en selecteer in de lijst met beschik bare claims de optie **given_name** en **family_name**

     ![optionele claims toevoegen](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Klik op **Toevoegen**.
10. Als u een waarschuwing over machtigingen krijgt, zoals hieronder wordt weer gegeven, schakelt u het selectie vakje in en selecteert u **toevoegen**.

     ![machtigingen voor optionele claims toevoegen](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Wanneer een gebruiker zich weer aanmeldt met de melding ' Aanmelden ' om uw verifieer bare referentie te krijgen, weet de VC Wallet-app ook de specifieke claims via de bereik parameter die moet worden geschreven naar de verifieer bare referentie.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Nieuwe VC maken met dit regel bestand en het oude weergave bestand

1. Het nieuwe regel bestand uploaden naar onze container
1. Maak op de pagina verifieer bare referenties een nieuwe referentie met de naam **modifiedCredentialExpert** met behulp van het oude weergave bestand en het nieuwe regel bestand (**modified-credentialExpert.jsaan**).
1. Nadat het proces voor het maken van de referentie is voltooid op de **overzichts** pagina, kopieert u de URL van de **uitgifte referentie** en slaat u deze op omdat deze in de volgende sectie nodig is.

## <a name="before-we-continue"></a>Voordat we verdergaan

We moeten enkele waarden samen stellen voordat we de benodigde code wijzigingen kunnen aanbrengen. We gebruiken deze waarden in de volgende sectie om te zorgen dat de voorbeeld code uw eigen sleutels gebruikt die zijn opgeslagen in uw kluis. Tot nu toe moeten de volgende waarden gereed zijn.

- De **contract-URI** van de referentie die we zojuist hebben gemaakt (URL voor de uitgifte referentie)
- **Client-id van toepassing** We hebben dit gedaan toen we de node-app hebben geregistreerd.
- **Client geheim** U hebt dit eerder gemaakt toen u uw app-toegang tot de sleutel kluis hebt verleend.

Er zijn enkele andere waarden die we nodig hebben om de wijzigingen in de voor beeld-app één keer in te voeren. Laten we dat nu doen.

### <a name="verifiable-credentials-settings"></a>Instellingen voor Controleer bare referenties

1. Navigeer naar de pagina verifieer bare referenties en kies **instellingen**.  
1. Kopieer de volgende waarden:

    - Tenant-id 
    - Id van de verlener (uw was)
    - Sleutel kluis (URI)

1. Onder de id van de handtekening sleutel bevindt zich een URI, maar er is slechts een deel ervan nodig. Kopieer een kopie van het onderdeel met de tekst **issuerSigningKeyION** zoals gemarkeerd door de rode rechthoek in de onderstaande afbeelding.

   ![aanmeldings sleutel-id](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Document

1. Open de [DIF-Ion-netwerk Verkenner](https://identity.foundation/ion/explorer/)

2. Plak het in de zoek balk.

4. Zoek in het opgemaakte antwoord de sectie met de naam **verificationMethod**
5. Kopieer de id onder ' verificationMethod ' en voorzie deze als de kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Nu hebben we alles nodig om de wijzigingen aan te brengen in de voorbeeld code.

- **Verlener:** app.js update Const-referentie met uw nieuwe contract-URI
- **Verifier:** app.js de issuerDid bij met de id van de certificaat verlener
- De **Uitgever en Verifier** updaten de didconfig.jsmet de volgende waarden:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Dit is een demo toepassing en u moet uw toepassing normaal gesp roken nooit rechtstreeks aan het geheim toewijzen.


Nu hebt u alles in handen om uw eigen Controleer bare referentie van uw Azure Active Directory-Tenant met uw eigen sleutels te verlenen en te controleren. 

## <a name="issue-and-verify-the-vc"></a>Het VC uitgeven en verifiëren

Volg dezelfde stappen die we in de vorige zelf studie hebben gevolgd om de verifieer bare referentie uit te geven en te valideren met uw app. Zodra u het verificatie proces hebt voltooid, bent u klaar om verder te gaan met het controleren op Controleer bare referenties.

1. Open een opdracht prompt en open de map met de uitgever.
2. Voer de bijgewerkte node-app uit.

    ```terminal
    node app.js
    ```

3. Met een andere opdracht prompt voert u ngrok uit om een URL in te stellen op 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > U ziet mogelijk ook een waarschuwing dat deze app of website riskant kan zijn. Er wordt op dit moment een bericht verwacht omdat u uw domein nog niet hebt gekoppeld. Volg de [DNS-bindings](how-to-dnsbind.md) instructies om dit te configureren.

    
4. Open de HTTPS-URL die door ngrok is gegenereerd.

    ![NGROK forwarding-eind punten](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Kies **referentie ophalen**
6. Scan in verificator de QR-code.
7. Bij **deze app of website kan** waarschuwings bericht over Risk ante waarschuwing **kiezen.**

  ![Eerste waarschuwing](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Op de waarschuwing Risk ante website kiest **u toch door gaan (onveilig)**

  ![Tweede waarschuwing over de uitgever](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. In het scherm **een referentie toevoegen** ziet u enkele dingen: 
    1. Boven aan het scherm ziet u een rood **niet-geverifieerd** bericht
    1. De referentie wordt aangepast op basis van de wijzigingen die u in het weergave bestand hebt aangebracht.
    1. De optie **Aanmelden bij uw account** verwijst naar de aanmeldings pagina van Azure AD.
    
   ![Referentie scherm toevoegen met waarschuwing](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Kies **Aanmelden bij uw account** en verificatie met behulp van een gebruiker in uw Azure AD-Tenant.
11. Nadat de verificatie van de knop **toevoegen** is voltooid, wordt deze niet meer grijs weer gegeven. Kies **toevoegen**.

  ![Referentie scherm toevoegen na verificatie](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

We hebben nu een verifieer bare referentie verleend met onze Tenant om onze VC te genereren terwijl we nog steeds onze B2C-Tenant gebruiken voor verificatie.

  ![VC dat is uitgegeven door uw Azure AD en dat is geverifieerd door ons Azure B2C-exemplaar](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testen van het VC controleren met behulp van de voor beeld-app

Nu we de verifieer bare referentie van onze eigen Tenant met claims van uw Azure AD hebben verstrekt, kunt u deze controleren met behulp van de voor beeld-app.

1. Stop met het uitvoeren van de ngrok-service van de verlener.

    ```cmd
    control-c
    ```

2. Voer nu ngrok uit met de Verifier-poort 8082.

    ```cmd
    ngrok http 8082
    ```

3. In een ander Terminal venster gaat u naar de Verifier-app en voert u deze uit op dezelfde manier als de uitgever van de app.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Open de ngrok-URL in uw browser en scan de QR-code met behulp van verificator op uw mobiele apparaat.
5. Kies op het mobiele apparaat de optie **toestaan** op het scherm **nieuwe machtigings aanvraag** .

   >[!IMPORTANT]
    > Omdat de voor beeld-app ook de aanvraag voor de presentatie heeft ondertekend, ziet u een waarschuwing dat deze app of website riskant kan zijn. Er wordt op dit moment een bericht verwacht omdat u uw domein nog niet hebt gekoppeld. Volg de [DNS-bindings](how-to-dnsbind.md) instructies om dit te configureren.
    
   ![nieuwe machtigings aanvraag](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. U hebt nu de referentie geverifieerd en de website moet uw voor-en achternaam van uw Azure AD-gebruikers account weer geven. 

U kunt de zelf studie nu volt ooien en officieel een geverifieerde referentie expert zijn. Uw voor beeld-app gebruikt uw voor het verlenen en verifiëren van de voor beelden, terwijl u claims schrijft naar een verifieer bare referentie van uw Azure AD. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van [aangepaste referenties](credential-design.md)
- Communicatie [voorbeelden](issuer-openid.md) van de uitgevers service
