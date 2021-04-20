---
title: 'Zelfstudie: Verifieerbare referenties uitgeven en verifiëren met behulp van uw Azure-tenant (preview)'
description: Wijzig het codevoorbeeld Verifiable Credential om te werken met uw Azure-tenant
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739220"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Zelfstudie: Verifieerbare referenties uitgeven en verifiëren met behulp van uw tenant (preview)

> [!IMPORTANT]
> Azure Active Directory verifieerbare referenties is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Nu u uw Azure-tenant hebt ingesteld met de verifieerbare referentieservice, doorlopen we de stappen die nodig zijn om uw Azure Active Directory (Azure AD) in staat te stellen verifieerbare referenties uit te geven met behulp van de voorbeeld-app.

In dit artikel gaat u het volgende doen:

> [!div class="checklist"]
> * De voorbeeld-app registreren in uw Azure AD
> * Een regel maken en een bestand weergeven
> * Regels uploaden en bestanden weergeven
> * De service Verifiable Credentials Issuer instellen voor het gebruik van Azure Key Vault
> * Werk voorbeeldcode bij met de gegevens van uw tenant.

Voor onze voorbeeldcode moeten gebruikers zich verifiëren bij een id-provider, met name Azure AD B2C, voordat de geverifieerde referentieexpert kan worden uitgegeven. Niet alle verifieerbare verwerkers van referenties vereisen verificatie voordat referenties worden uitgeven.

Door id-tokens te authenticeren, kunnen gebruikers bewijzen wie ze zijn voordat ze hun referenties ontvangen. Wanneer gebruikers zich aanmelden, retourneert de id-provider een beveiliging token met claims over de gebruiker. De verlenerservice transformeert deze beveiligingstokens en hun claims vervolgens in een verifieerbare referentie. De verifieerbare referentie is ondertekend met de DID van de vergever.

Elke id-provider die het OpenID Connect ondersteunt, wordt ondersteund. Voorbeelden van ondersteunde id-providers [zijn Azure Active Directory](../fundamentals/active-directory-whatis.md), [en Azure AD B2C](../../active-directory-b2c/overview.md). In deze zelfstudie gebruiken we AAD.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgenomen dat u de stappen in de vorige zelfstudie al [hebt](enable-your-tenant-verifiable-credentials.md) voltooid en toegang hebt tot de omgeving die u hebt gebruikt.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Een app registreren om DID-wallets in te stellen om gebruikers aan te melden

Als u een verifieerbare referentie wilt uitgeven, moet u een app registreren, zodat Authenticator of een andere verifieerbare referentie-wallet gebruikers kan aanmelden.  

Registreer een toepassing met de naam 'VC Wallet App' in Azure AD en verkrijg een client-id.

1. Volg de instructies voor het registreren van een toepassing [bij Azure AD](../develop/quickstart-register-app.md) gebruik de onderstaande waarden.

   - Naam: 'VC Wallet-app'
   - Ondersteunde accounttypen: alleen accounts in deze organisatiemap
   - Omleidings-URI: vcclient://openid/

   ![een toepassing registreren](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Nadat u de toepassing hebt geregistreerd, schrijft u de toepassings-id (client) op. U hebt deze waarde later nog nodig.

   ![client-id van toepassing](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Selecteer de **knop Eindpunten en** kopieer de URI van OpenID Connect metagegevensdocument. U hebt deze informatie nodig voor de volgende sectie. 

   ![eindpunten van vereengever](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Uw knooppunt-app instellen met toegang tot Azure Key Vault

Om de aanvraag voor het uitgeven van referenties van een gebruiker te verifiëren, gebruikt de verlenerwebsite uw cryptografische sleutels in Azure Key Vault. Voor toegang Azure Key Vault heeft uw website een client-id en clientgeheim nodig die kunnen worden gebruikt voor verificatie bij Azure Key Vault.

1. Selecteer certificaten en geheimen tijdens het weergeven van de **overzichtspagina van de VC-wallet&.**
    ![certificaten en geheimen](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Kies in **de sectie Clientgeheimen** de **optie Nieuw clientgeheim**
    1. Voeg een beschrijving toe, zoals 'Node VC client secret'
    1. Verloopt: over één jaar.
  ![Toepassingsgeheim met een vervaldatum van één jaar](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Kopieer het GEHEIM. U hebt deze informatie nodig om uw voorbeeld-knooppunt-app bij te werken.

>[!WARNING]
> U hebt één kans om het geheim te kopiëren. Het geheim is één manier om ert achter te komen. Kopieer de id niet. 

Nadat u uw toepassing en clientgeheim in Azure AD hebt gemaakt, moet u de toepassing de benodigde machtigingen verlenen om bewerkingen uit te voeren op uw Key Vault. Het aanbrengen van deze machtigingswijzigingen is vereist om de website toegang te geven tot de persoonlijke sleutels die daar zijn opgeslagen.

1. Ga naar Key Vault.
2. Selecteer de sleutelkluis die we gebruiken voor deze zelfstudies.
3. Toegangsbeleid **kiezen** in het linkernavigatiebalk
4. Kies **+Toegangsbeleid toevoegen.**
5. Kies in **de sectie Sleutelmachtigingen** de optie **Get** en **Sign.**
6. Selecteer **Principal en** gebruik de toepassings-id om te zoeken naar de toepassing die we eerder hebben geregistreerd. Selecteer deze.
7. Selecteer **Toevoegen**.
8. Kies **OPSLAAN.**

Lees de RBAC-handleiding Key Vault key vault voor meer informatie over Key Vault [en toegangsbeheer](../../key-vault/general/rbac-guide.md)

![sleutelkluismachtigingen toewijzen](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Wijzigingen aanbrengen die overeenkomen met uw omgeving

Tot nu toe hebben we met onze voorbeeld-app gewerkt. De app maakt [gebruik van Azure Active Directory B2C](../../active-directory-b2c/overview.md) en we schakelen nu over op het gebruik van Azure AD. Daarom moeten we enkele wijzigingen aanbrengen die niet alleen overeenkomen met uw omgeving, maar ook ter ondersteuning van aanvullende claims die niet eerder zijn gebruikt.

1. Kopieer het onderstaande regelbestand en sla het opmodified-expertRules.js **op**. 

    > [!NOTE]
    > **'scope': 'openid profile'** is opgenomen in dit regelbestand en is niet opgenomen in het bestand Regels van de voorbeeld-app. In de volgende sectie wordt uitgelegd hoe u de optionele claims in uw Azure Active Directory inschakelen. 
    
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

2. Open het bestand en vervang de **waarden client_id** **configuratie** door de twee waarden die we in de vorige sectie hebben gekopieerd.

    ![de twee waarden markeren die moeten worden gewijzigd als onderdeel van deze stap](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  De waarde **Configuratie** is de URI OpenID Connect metagegevensdocument.

  Omdat de voorbeeldcode gebruik maakt van Azure Active Directory B2C en we Azure Active Directory gebruiken, moeten we optionele claims toevoegen via bereiken om ervoor te zorgen dat deze claims worden opgenomen in het id-token om te worden geschreven naar de verifieerbare referentie. 

3. Open Azure Portal in de Azure Active Directory.
4. Kies **App-registraties**.
5. Open de app VC Wallet die we eerder hebben gemaakt.
6. Kies **Tokenconfiguratie.**
7. Kies **+ Optionele claim toevoegen**

     ![voeg onder tokenconfiguratie een optionele claim toe](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Kies **id bij Tokentype** en kies in de lijst met beschikbare claims  **given_name** en **family_name**

     ![optionele claims toevoegen](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Klik op **Toevoegen**.
10. Als u een machtigingswaarschuwing krijgt zoals hieronder wordt weergegeven, schakel dan het selectievakje in en selecteer **Toevoegen.**

     ![machtigingen toevoegen voor optionele claims](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Wanneer een gebruiker nu de aanmelding krijgt te zien om uw verifieerbare referentie te krijgen, weet de VC Wallet-app dat de specifieke claims moeten worden opgeslagen via de bereikparameter die moet worden weggeschreven naar de verifieerbare referentie.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Nieuwe VC maken met dit regelbestand en het oude weergavebestand

1. Het nieuwe regelbestand uploaden naar onze container
1. Maak op de pagina Verifieerbare referenties een nieuwe referentie met **de naam** **modifiedCredentialExpert** met behulp van het oude weergavebestand en het nieuwemodified-credentialExpert.jsop .
1. Nadat het referentieproces is  voltooid vanaf de pagina Overzicht, kopieert u de **URL** voor referentieprobleem en sla deze op omdat we deze in de volgende sectie nodig hebben.

## <a name="before-we-continue"></a>Voordat we doorgaan

We moeten een aantal waarden samenbrengen voordat we de benodigde codewijzigingen kunnen aanbrengen. We gebruiken deze waarden in de volgende sectie om ervoor te zorgen dat de voorbeeldcode uw eigen sleutels gebruikt die zijn opgeslagen in uw kluis. Tot nu toe moeten de volgende waarden gereed zijn.

- **Contract-URI** van de referentie die we zojuist hebben gemaakt (URL van referentieprobleem)
- **Client-id van toepassing** We hebben deze toen we de Node-app registreerden.
- **Clientgeheim** We hebben deze eerder gemaakt toen we uw app toegang verleenden tot de sleutelkluis.

Er zijn nog enkele andere waarden die we moeten krijgen voordat we de wijzigingen één keer in onze voorbeeld-app kunnen aanbrengen. Laten we deze nu gaan halen.

### <a name="verifiable-credentials-settings"></a>Verifieerbare referentie-instellingen

1. Navigeer naar de pagina Verifieerbare referenties en kies **Instellingen.**  
1. Kopieer de volgende waarden:

    - Tenant-id 
    - Issuer identifier (uw DID)
    - Sleutelkluis (URI)

1. Onder de id van de ondertekeningssleutel is er een URI, maar we hebben er slechts een deel van nodig. Kopieer uit het deel met **de tekst issuerSigningKeyION,** zoals gemarkeerd door de rode rechthoek in de onderstaande afbeelding.

   ![sleutel-id voor aanmelden](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID-document

1. Open de [DIF ION-Netwerkverkenner](https://identity.foundation/ion/explorer/)

2. Plak uw DID in de zoekbalk.

4. Zoek in het opgemaakte antwoord de sectie met de **naam verificationMethod**
5. Kopieer onder verificationMethod de id en label deze als de kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Nu hebben we alles wat we nodig hebben om de wijzigingen in onze voorbeeldcode aan te brengen.

- **Verlener:** app.js const-referenties bijwerken met uw nieuwe contract-URI
- **Verifier:** app.js issuerDid bijwerken met uw verificator-id
- **Vergever en Verifier** werken de didconfig.jsbij met de volgende waarden:


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
>Dit is een demotoepassing en normaal gesproken moet u uw toepassing nooit rechtstreeks het geheim geven.


U hebt nu alles wat u kunt uitgeven en uw eigen verifieerbare referentie van uw Azure Active Directory tenant met uw eigen sleutels kunt verifiëren. 

## <a name="issue-and-verify-the-vc"></a>Probleem met en verificatie van de VC

Volg dezelfde stappen die we in de vorige zelfstudie hebben gevolgd om de verifieerbare referentie uit te geven en deze te valideren met uw app. Zodra u het verificatieproces hebt voltooid, bent u klaar om verder te leren over verifieerbare referenties.

1. Open een opdrachtprompt en open de map issuer.
2. Voer de bijgewerkte knooppunt-app uit.

    ```terminal
    node app.js
    ```

3. Voer met behulp van een andere opdrachtprompt ngrok uit om een URL in te stellen op 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > U ziet mogelijk ook een waarschuwing dat deze app of website riskant kan zijn. Het bericht wordt op dit moment verwacht omdat uw DID nog niet aan uw domein is gekoppeld. Volg de [instructies voor DNS-binding](how-to-dnsbind.md) om dit te configureren.

    
4. Open de HTTPS-URL die is gegenereerd door ngrok.

    ![NGROK-doorsturende eindpunten](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Kies **GET CREDENTIAL**
6. Scan in Authenticator de QR-code.
7. Op **Deze app of website kan riskant waarschuwingsbericht** worden weergegeven, kiest u **Geavanceerd.**

  ![Eerste waarschuwing](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Kies toch doorgaan **(onveilig)** bij de waarschuwing over riskante websites

  ![Tweede waarschuwing over de vergever](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. In het **scherm Een referentie toevoegen** ziet u een aantal dingen: 
    1. Boven aan het scherm ziet u een rood **bericht Dat niet geverifieerd**
    1. De referentie wordt aangepast op basis van de wijzigingen die we hebben aangebracht in het weergavebestand.
    1. De **optie Aanmelden bij uw account** verwijst naar de aanmeldingspagina van Azure AD.
    
   ![referentiescherm met waarschuwing toevoegen](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Kies **Aanmelden bij uw account en** verifieert u met behulp van een gebruiker in uw Azure AD-tenant.
11. Nadat de authenticatie van de **knop Toevoegen** is geslaagd, wordt deze niet meer grijs. Kies **Toevoegen.**

  ![referentiescherm toevoegen na de authenticatie](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

We hebben nu een verifieerbare referentie uitgegeven met behulp van uw tenant om uw vc te genereren terwijl u nog steeds de oorspronkelijke B2C-tenant gebruikt voor verificatie.

  ![vc uitgegeven door uw Azure AD en geverifieerd door onze Azure B2C-instantie](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>De VC testen met behulp van de voorbeeld-app

Nu we de verifieerbare referentie van uw eigen tenant met claims van uw Azure AD hebben uitgegeven, gaan we deze controleren met behulp van de voorbeeld-app.

1. Stop de ngrok-service voor de verlener.

    ```cmd
    control-c
    ```

2. Voer nu ngrok uit met de verifierpoort 8082.

    ```cmd
    ngrok http 8082
    ```

3. Navigeer in een ander terminalvenster naar de verifier-app en voer deze op dezelfde manier uit als de manier waarop we de vergever-app hebben uitgevoerd.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Open de URL ngrok in uw browser en scan de QR-code met authenticator op uw mobiele apparaat.
5. Kies op uw mobiele apparaat Toestaan **in het** scherm **Nieuwe machtigingsaanvraag.**

   >[!IMPORTANT]
    > Omdat de voorbeeld-app ook uw DID gebruikt om de presentatieaanvraag te ondertekenen, ziet u een waarschuwing dat deze app of website riskant kan zijn. Het bericht wordt op dit moment verwacht, omdat we uw DID nog niet aan uw domein hebben gekoppeld. Volg de [instructies voor DNS-binding](how-to-dnsbind.md) om dit te configureren.
    
   ![nieuwe machtigingsaanvraag](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. U hebt nu uw referenties geverifieerd en op de website moeten uw voor- en achternaam worden weergegeven vanuit het gebruikersaccount van uw Azure AD. 

U hebt nu de zelfstudie doorlopen en bent officieel geverifieerd referentieexpert. Uw voorbeeld-app gebruikt uw DID voor zowel het verlenen als verifiëren van, terwijl claims worden geschreven in een verifieerbare referentie van uw Azure AD. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken [van aangepaste referenties](credential-design.md)
- Voorbeelden van servicecommunicatie van [verlener](issuer-openid.md)
