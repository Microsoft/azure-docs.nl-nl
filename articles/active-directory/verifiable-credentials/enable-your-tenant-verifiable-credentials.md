---
title: 'Zelf studie: uw Azure Active Directory configureren voor het uitgeven van verifieer bare referenties (preview-versie)'
description: In deze zelf studie bouwt u de omgeving die nodig is voor het implementeren van verifieer bare referenties in uw Tenant
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 03/31/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 08aaa49f73ed437e041ffb93dc9ef5be41e316ec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106171933"
---
# <a name="tutorial-configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Zelf studie: uw Azure Active Directory configureren voor het uitgeven van verifieer bare referenties (preview-versie)

In deze zelf studie bouwen we voort op het werk dat u hebt gedaan als onderdeel van het artikel [aan](get-started-verifiable-credentials.md) de slag en stelt u uw Azure Active Directory (Azure AD) in met een eigen [gedecentraliseerde id](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (was). We gebruiken de gecentraliseerde id om een verifieer bare referentie uit te geven met behulp van de voor beeld-app en uw certificaat verlener. in deze zelf studie gebruiken we echter nog steeds de voor beeld-B2C-Tenant van Azure voor authenticatie.  In onze volgende zelf studie nemen we extra stappen voor het ophalen van de app die is geconfigureerd voor gebruik met uw Azure AD.

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel:

> [!div class="checklist"]
> * U maakt de benodigde services voor het voorbereiden van uw Azure AD voor Controleer bare referenties 
> * We maken uw
> * De regels worden aangepast en de bestanden worden weer gegeven
> * Verifieer bare referenties configureren in azure AD.


## <a name="prerequisites"></a>Vereisten

Voordat u deze zelf studie kunt volt ooien, moet u eerst het volgende doen:

- Voltooi de [aan de slag](get-started-verifiable-credentials.md).
- U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure AD met een P2- [licentie](https://azure.microsoft.com/pricing/details/active-directory/). Volg de [instructies om een gratis ontwikkelaars account te maken](how-to-create-a-free-developer-account.md) als u er nog geen hebt.
- Een exemplaar van [Azure Key Vault](../../key-vault/general/overview.md) waarvoor u rechten hebt om sleutels en geheimen te maken.

## <a name="azure-active-directory"></a>Azure Active Directory

Voordat we kunnen beginnen, moeten we een Azure AD-Tenant hebben. Als uw Tenant is ingeschakeld voor verifieer bare referenties, wordt er een gecentraliseerde id (geslaagd) toegewezen en wordt deze ingeschakeld met een verlener-service voor het uitgeven van verifieer bare referenties. Elke Controleer bare referentie die u verstuurt, wordt uitgegeven door uw Tenant en de bijbehorende. De is ook gebruikt bij het controleren van Controleer bare referenties.
Als u zojuist een test-Azure-abonnement hebt gemaakt, hoeft uw Tenant niet te worden gevuld met gebruikers accounts, maar moet u ten minste één gebruikers test account hebben om latere zelf studies te volt ooien.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Wanneer u werkt met geverifieerde referenties, hebt u volledige controle over en beheer van de cryptografische sleutels die uw Tenant gebruikt om verifieer bare referenties digitaal te ondertekenen. Als u referenties wilt verlenen en verifiëren, moet u Azure AD voorzien van toegang tot uw eigen exemplaar van Azure Key Vault.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
2. Voer in het zoekvak **sleutel kluis** in.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Abonnement**: Kies een abonnement.
    - Kies onder **resource groep** de optie **nieuwe maken** en voer een naam voor de resource groep in, zoals **VC-Resource-Group**. De naam van de resource groep wordt in meerdere artikelen gebruikt.
    - **Naam**: geef een unieke naam op. We gebruiken **woodgroup-VC-KV** , dus vervang deze waarde door uw eigen unieke naam.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Nadat u de bovenstaande gegevens hebt opgegeven, selecteert u **toegangs beleid**

    ![een sleutel kluis pagina maken](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Kies in het scherm toegangs beleid de optie **toegangs beleid toevoegen**

    >[!NOTE]
    > Het account waarmee de sleutel kluis wordt gemaakt, is standaard de enige met Access. De verifieer bare referentie service moet toegang hebben tot de sleutel kluis. De sleutel kluis moet een toegangs beleid hebben waarmee de beheerder **sleutels kan maken**, de mogelijkheid hebben om **sleutels te verwijderen** als u **zich afmeldt** en u aan te melden om de domein binding te maken voor verifieer bare referenties. Als u hetzelfde account gebruikt tijdens het testen, moet u ervoor zorgen dat u het standaard beleid wijzigt om **het account aan te geven naast de** standaard machtigingen die aan kluis makers worden verleend.

8. Voor de gebruikers beheerder moet u ervoor zorgen dat in de sectie sleutel machtigingen **maken**, **verwijderen** en **ondertekenen** is ingeschakeld. Maken en verwijderen zijn standaard al ingeschakeld en u moet de enige sleutel machtiging hebben die moet worden bijgewerkt. 

    ![Key Vault machtigingen](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Selecteer **Controleren + maken**.
10. Selecteer **Maken**.
11. Ga naar de kluis en noteer de naam en URI van de kluis

Let op de onderstaande twee eigenschappen:

- **Kluis naam**: in het voor beeld is de waarde naam **Woodgrove-VC-KV**. U gebruikt deze naam voor andere stappen.
- **Kluis-URI**: in het voor beeld is deze waarde https://woodgrove-vc-kv.vault.azure.net/ . Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

>[!NOTE]
> Elke sleutel kluis transactie resulteert in extra kosten van het Azure-abonnement. Bekijk de [pagina met prijzen voor Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

>[!IMPORTANT]
> Tijdens de preview-versie van de Azure Active Directory verifieer bare referenties moeten de sleutels en geheimen die in de kluis zijn gemaakt, niet meer worden gewijzigd nadat deze is gemaakt. Als u sleutels en geheimen verwijdert, uitschakelt of bijwerkt, worden er verleende referenties ongeldig. Wijzig uw sleutels of geheimen niet tijdens de preview-versie.

## <a name="create-a-modified-rules-and-display-file"></a>Een gewijzigde regel maken en een bestand weer geven

In deze sectie gebruiken we de regels en weer geven bestanden van de voor beeld-Uitgever van de app en wijzigen ze enigszins om de eerste verifieer bare referentie van uw Tenant te maken.

1. Kopieer zowel de regels als json-bestanden weer geven naar een tijdelijke map en wijzig de naam ervan **MyFirstVC-rules.js** **MyFirstVC-display.js** respectievelijk. U kunt beide bestanden vinden onder **verlener \ issuer_config**

   ![bestanden weer geven en indelen in de voor beeld-app-map](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![bestanden in een tijdelijke map weer geven en bekijken](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Open de MyFirstVC-rules.jsin het bestand in de code-editor. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

We gaan nu het veld type wijzigen in "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Sla deze wijziging op.

 >[!NOTE]
   > We wijzigen de **configuratie** of de **client_id** op dit punt in de zelf studie niet. We gebruiken nog steeds de micro soft B2C-Tenant die we hebben gebruikt in de [aan](get-started-verifiable-credentials.md)de slag. We gebruiken uw Azure AD in de volgende zelf studie.

3. Open de MyFirstVC-display.jsin het bestand in de code-editor.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Hiermee kunnen enkele wijzigingen worden aangebracht, zodat deze verifieer bare referentie zichtbaar is op een andere manier dan de versie van de voorbeeld code. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Sla deze wijzigingen op.
## <a name="create-a-storage-account"></a>Een opslagaccount maken

Voordat u onze eerste verifieer bare referentie maakt, moet u een Blob Storage-container maken die onze configuratie-en regel bestanden kan bevatten.

1. Maak een opslag account met behulp van de opties die hieronder worden weer gegeven. Raadpleeg het artikel [een opslag account maken](../../storage/common/storage-account-create.md?tabs=azure-portal) voor gedetailleerde stappen.

   - **Abonnement:** Kies het abonnement dat wordt gebruikt voor deze zelf studies.
   - **Resource groep:** Kies dezelfde resource groep die we hebben gebruikt in eerdere zelf studies (**VC-Resource-Group**).
   - **Naam:**  Een unieke naam.
   - **Locatie:** (VS) vs-Oost.
   - **Prestaties:** Standaard.
   - **Soort account:** Opslag v2.
   - **Replicatie:** Lokaal redundant.
 
   ![Een nieuw opslagaccount maken](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Nadat het opslag account is gemaakt, moeten we een container maken. Selecteer **containers** onder **Blob Storage** en maak een container met behulp van de onderstaande waarden:

    - **Naam:** VC-container
    - **Openbaar toegangs niveau:** Privé (geen anonieme toegang)

   ![Een container maken](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Selecteer nu uw nieuwe container en upload zowel de nieuwe regels als de bestanden **MyFirstVC-display.jsop** en **MyFirstVC-rules.js** die eerder zijn gemaakt.

   ![regel bestand uploaden](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>BLOB-rol toewijzen

Voordat u de referentie maakt, moet u eerst de aangemelde gebruiker de juiste roltoewijzing geven om toegang te krijgen tot de bestanden in de opslag-blob.

1. Navigeer naar **opslag**  >  **container**.
2. Kies **Access Control (IAM)** in het menu aan de linkerkant.
3. Kies **roltoewijzingen**.
4. Selecteer **Toevoegen**.
5. Kies in de sectie **rol** de optie **Storage BLOB data Reader**.
6. Onder **toegang toewijzen om** **gebruikers-, groeps-of Service principe** te kiezen.
7. In **selecteren**: Kies het account dat u gebruikt om deze stappen uit te voeren.
8. Selecteer **Opslaan** om de roltoewijzing te volt ooien.


   ![Een roltoewijzing toevoegen](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Standaard krijgen container makers de rol **eigenaar** toegewezen. De rol van de **eigenaar** is niet voldoende. Uw account moet de rol van **BLOB-gegevens lezer voor opslag** hebben. Voor meer informatie raadpleegt u [de Azure Portal om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Verifieer bare Referenties instellen (preview-versie)

Nu moeten we de laatste stap uitvoeren om uw Tenant in te stellen voor Controleer bare referenties.

1. Zoek in het Azure Portal naar **verifieer bare referenties**.
2. Kies **verifieer bare referenties (preview-versie)**.
3. Kies **aan de slag**
4. U moet uw organisatie instellen en de naam, het domein en de sleutel kluis van de organisatie opgeven. Laten we eens kijken naar een van de waarden.

      - **organisatie naam**: deze naam verwijst naar uw bedrijf binnen de verifieer bare referentie service. Deze waarde is niet klant gericht.
      - **Domein:** Het ingevoerde domein wordt toegevoegd aan een service-eind punt in het document. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) en andere Wallets gebruiken deze informatie om te controleren of uw was [gekoppeld aan uw domein](how-to-dnsbind.md). Als de Wallet de gewiste kan controleren, wordt een gecontroleerd symbool weer gegeven. Als de Wallet niet kan verifiëren, wordt de gebruiker ervan op de hoogte gebracht dat de referentie is uitgegeven door een organisatie die niet kan worden gevalideerd. Het domein is wat u verbindt met een bedoelingen die de gebruiker voor uw bedrijf kan weten.
      - **Sleutel kluis:** Geef de naam op van de Key Vault die u eerder hebt gemaakt.
 
   >[!IMPORTANT]
   > Het domein kan geen omleiding zijn, anders is het domein en kan niet worden gekoppeld. Zorg ervoor dat u de https://www.domain.com indeling gebruikt.
    
5. Kies **opslaan en referentie maken**

    ![de identiteit van uw organisatie instellen](media/enable-your-tenant-verifiable-credentials/save-create.png)

Gefeliciteerd, uw Tenant is nu ingeschakeld voor de preview-versie van de geverifieerde referenties.

## <a name="create-your-vc-in-the-portal"></a>Uw VC maken in de portal

In de vorige stap verlaat u de pagina **een nieuwe referentie maken** . 

   ![Controleer bare referenties aan de slag](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Voeg onder referentie naam de naam **MyFirstVC** toe. Deze naam wordt gebruikt in de portal om uw verifieer bare referenties te identificeren en is opgenomen als onderdeel van het Controleer bare referentie contract.
2. Kies in het gedeelte weergave bestand de optie **weergave bestand configureren**
3. Selecteer in de sectie **opslag accounts** de optie **woodgrovevcstorage**.
4. Kies in de lijst met beschik bare containers **VC-container**.
5. Kies de **MyFirstVC-display.jsvoor** het bestand dat u eerder hebt gemaakt.
6. Kies in de sectie **een nieuwe referentie maken** in het gedeelte **regels** bestand **configureren regels bestand**
7. Selecteer in de sectie **opslag accounts** de optie **woodgrovecstorage**
8. Kies **VC-container**.
9. Selecteer **MyFirstVC-rules.jsop**
10. Kies **maken** in het scherm **een nieuwe referentie maken** .

   ![een nieuwe referentie maken](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Referentie-URL

Nu u een nieuwe referentie hebt, kopieert u de Referentie-URL.

   ![De URL van de probleem referentie](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>De Referentie-URL is de combi natie van regels en weergave bestanden. Het is de URL die door de verificator wordt geëvalueerd voordat deze wordt weer gegeven aan de door de gebruiker geverifieerde vereisten voor de uitgifte van referenties.  

## <a name="update-the-sample-app"></a>De voor beeld-app bijwerken

Nu gaan we wijzigingen aanbrengen in de uitgever code van de voor beeld-app om deze bij te werken met uw Controleer bare Referentie-URL. Hierdoor kunt u verifieer bare referenties verlenen met uw eigen Tenant.

1. Ga naar de map waarin u de bestanden van de voor beeld-app hebt geplaatst.
2. Open de map met de certificaat verlener en open app.js in Visual Studio code.
3. Werk de constante referentie bij met uw nieuwe referentie-URL en stel de credentialType-constante in op ' MyFirstVC ' en sla het bestand op.

    ![afbeelding van Visual Studio code waarin de relevante gebieden zijn gemarkeerd](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Open een opdracht prompt en open de map met de uitgever.
5. Voer de bijgewerkte node-app uit.

    ```terminal
    node app.js
    ```

6. Met een andere opdracht prompt voert u ngrok uit om een URL in te stellen op 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > U ziet mogelijk ook een waarschuwing dat deze app of website riskant kan zijn. Er wordt op dit moment een bericht verwacht omdat u uw domein nog niet hebt gekoppeld. Volg de [DNS-bindings](how-to-dnsbind.md) instructies om dit te configureren.

    
7. Open de HTTPS-URL die door ngrok is gegenereerd.

    ![NGROK forwarding-eind punten](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Kies **referentie ophalen**
9. Scan in verificator de QR-code.
10. Bij **deze app of website kan** waarschuwings bericht over Risk ante waarschuwing **kiezen.**

  ![Eerste waarschuwing](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. Op de waarschuwing Risk ante website kiest **u toch door gaan (onveilig)**

  ![Tweede waarschuwing over de uitgever](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. In het scherm **een referentie toevoegen** ziet u enkele dingen: 
    1. Boven aan het scherm ziet u een rood **niet-geverifieerd** bericht
    1. De referentie wordt aangepast op basis van de wijzigingen die u in het weergave bestand hebt aangebracht.
    1. De optie **Aanmelden bij uw account** verwijst naar **didplayground.b2clogin.com**.
    
   ![Referentie scherm toevoegen met waarschuwing](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Kies **Aanmelden bij uw account** en verificatie met behulp van de referentie gegevens die u hebt ingevoerd in de [zelf studie aan de slag](get-started-verifiable-credentials.md).
14. Nadat de verificatie van de knop **toevoegen** is voltooid, wordt deze niet meer grijs weer gegeven. Kies **toevoegen**.

  ![Referentie scherm toevoegen na verificatie](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

We hebben nu een verifieer bare referentie verleend met onze Tenant om onze VC te genereren terwijl we nog steeds onze B2C-Tenant gebruiken voor verificatie.

  ![VC dat is uitgegeven door uw Azure AD en dat is geverifieerd door ons Azure B2C-exemplaar](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testen van het VC controleren met behulp van de voor beeld-app

Nu we de verifieer bare referentie van onze eigen Tenant hebben uitgegeven, gaan we deze verifiëren met behulp van de voor beeld-app.

>[!IMPORTANT]
> Gebruik bij het testen hetzelfde e-mail adres en wacht woord dat u hebt gebruikt tijdens het artikel [aan](get-started-verifiable-credentials.md) de slag. Terwijl uw Tenant het VC uitgeeft, is de invoer afkomstig van een id-token dat is uitgegeven door de micro soft B2C-Tenant. In de zelf studie twee wordt de uitgifte van tokens omgeschakeld naar uw Tenant

1. Open de **instellingen** op de Blade Controleer bare referenties in de Azure Portal. De gecentraliseerde id (DID) kopiëren.

   ![de DID kopiëren](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Open nu de map Verifier van de voor beeld-app-bestanden. We moeten het app.js-bestand bijwerken in de controle code van de Verifier en de volgende wijzigingen aanbrengen:

    - **referentie**: Wijzig de Referentie-URL
    - **credentialType**: ' MyFirstVC '
    - **issuerDid**: Kopieer deze waarde van Azure Portal>Controleer bare referenties (Preview) >instellingen>gedecentraliseerde id (was)
    
   ![de constante issuerDid bijwerken zodat deze overeenkomt met de id van de uitgever](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Stop met het uitvoeren van de ngrok-service van de verlener.

    ```cmd
    control-c
    ```

4. Voer nu ngrok uit met de Verifier-poort 8082.

    ```cmd
    ngrok http 8082
    ```

5. In een ander Terminal venster gaat u naar de Verifier-app en voert u deze uit op dezelfde manier als de uitgever van de app.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Open de ngrok-URL in uw browser en scan de QR-code met behulp van verificator op uw mobiele apparaat.
7. Kies op het mobiele apparaat de optie **toestaan** op het scherm **nieuwe machtigings aanvraag** .

    >[!NOTE]
    > Het ondertekenen van deze VC is nog steeds de micro soft-B2C. De Verifier bevindt zich nog steeds van de micro soft-voor beeld-app-Tenant. Aangezien micro soft is gekoppeld aan een domein waarvan wij eigenaar zijn, ziet u de waarschuwing niet zoals we hebben gehad tijdens de uitgifte stroom. Dit wordt in de volgende sectie bijgewerkt.
    
   ![nieuwe machtigings aanvraag](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Uw referentie is niet geverifieerd.

## <a name="next-steps"></a>Volgende stappen

Nu u beschikt over de voorbeeld code voor het uitgeven van een VC van uw certificaat verlener, kunt u door gaan naar de volgende sectie waar u uw eigen id-provider gebruikt om gebruikers te verifiëren die verifieer bare referenties proberen op te halen en uw aanmeldings verzoeken voor presentaties te ondertekenen.

> [!div class="nextstepaction"]
> [Zelf studie: een probleem en controleer verifieer bare referenties met uw Tenant](issue-verify-verifiable-credentials-your-tenant.md)


