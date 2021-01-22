---
title: Zelfstudie - Kenmerktoewijzingen van Azure Active Directory aanpassen
description: Lees hier wat kenmerktoewijzingen voor SaaS-apps in Azure Active Directory zijn en hoe u ze kunt aanpassen zodat ze voldoen aan uw bedrijfsbehoeften.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: kenwith
ms.openlocfilehash: efdbec10c74a6b1892df13b8308538e61f42f679
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673498"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Zelfstudie - Kenmerktoewijzingen voor het inrichten van gebruikers aanpassen voor SaaS-toepassingen in Azure Active Directory

Microsoft Azure AD biedt ondersteuning voor het inrichten van gebruikers voor SaaS-toepassingen van derden, zoals Sales Force en G suite. Als u het inrichten van gebruikers inschakelt voor een SaaS-toepassing van derden, worden de kenmerkwaarden van de toepassing in de Azure-portal bepaalt via kenmerktoewijzingen.

Er zijn vooraf geconfigureerde sets met kenmerken en kenmerktoewijzingen beschikbaar voor de koppeling tussen gebruikersobjecten van Azure AD en van de SaaS-app. Sommige apps beheren andere typen objecten naast gebruikersobjecten, zoals objecten voor groepen.

U kunt de standaardtoewijzingen van kenmerken aanpassen op basis van de behoeften van uw bedrijf. Dit betekent dat u bestaande kenmerktoewijzingen kunt wijzigen of verwijderen, of nieuwe kenmerktoewijzingen kunt maken.

## <a name="editing-user-attribute-mappings"></a>Kenmerktoewijzingen voor gebruikers wijzigen

Volg deze stappen om toegang te krijgen tot de functie **Toewijzingen** voor het inrichten van gebruikers:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com).
1. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
1. Selecteer een app om verschillende deelvensters voor de app weer te geven, waaronder het deelvenster Beheren. Hier kunt u rapporten bekijken en app-instellingen beheren.
1. Selecteer **Inrichten** om de instellingen voor het inrichten van gebruikersaccounts voor de geselecteerde app te beheren.
1. Vouw **Toewijzingen** uit om de gebruikerskenmerken weer te geven en te bewerken die zijn gekoppeld tussen Azure AD en de doeltoepassing. Als de doeltoepassing dit ondersteunt, kunt u hier desgewenst het inrichten van groepen en gebruikersaccounts configureren.

   ![Gebruik Toewijzingen om gebruikerskenmerken weer te geven en te bewerken](./media/customize-application-attributes/21.png)

1. Selecteer een configuratie in **Toewijzingen** om het bijbehorende scherm **Kenmerktoewijzing** te openen. Sommige kenmerktoewijzingen zijn vereist voor de juiste werking van een SaaS-toepassing. Voor vereiste kenmerken is de functie **Verwijderen** niet beschikbaar.

   ![Gebruik Kenmerktoewijzing om kenmerktoewijzingen voor apps te configureren](./media/customize-application-attributes/22.png)

   In deze schermopname ziet u dat het kenmerk **Username** van een beheerd object in Salesforce de waarde krijgt van **userPrincipalName** van het gekoppelde Azure Active Directory-object.

1. Selecteer een bestaande **kenmerktoewijzing** om het scherm **Kenmerk bewerken** te openen. Hier kunt u de gebruikerskenmerken bewerken die zijn gekoppeld tussen Azure AD en de doeltoepassing.

   ![Gebruik het scherm Kenmerk bewerken om kenmerken van gebruikers te bewerken](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informatie over typen kenmerktoewijzingen

Met kenmerktoewijzingen bepaalt u hoe kenmerken een waarde krijgen in een SaaS-toepassing van derden.
Er worden vier verschillende toewijzingstypen ondersteund:

- **Direct**: het doelkenmerk krijgt de waarde van een kenmerk van het gekoppelde object in Azure AD.
- **Constante**: het doelkenmerk wordt gevuld met een specifieke tekenreeks die u hebt opgegeven.
- **Expressie**: het doelkenmerk wordt ingevuld op basis van het resultaat van een scriptachtige expressie.
  Zie [Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md) voor meer informatie.
- **Geen**: het doelkenmerk blijft ongewijzigd. Als het doelkenmerk echter leeg is, wordt het ingevuld met de standaardwaarde die u opgeeft.

Naast deze vier basistypen kunt u via ondersteuning voor aangepaste kenmerktoewijzingen het concept van toewijzing van een optionele **standaardwaarde** gebruiken. De toewijzing van een standaardwaarde zorgt ervoor dat een doelkenmerk wordt ingevuld met een waarde als er geen waarde beschikbaar is in Azure AD of in het doelobject. De meest gangbare configuratie is om dit leeg te laten.

### <a name="understanding-attribute-mapping-properties"></a>Informatie over eigenschappen van kenmerktoewijzingen

In de vorige paragraaf hebt u kennisgemaakt met de verschillende typen kenmerktoewijzingen.
Hier beschrijven we de kenmerken die u kunt gebruiken met kenmerktoewijzingen:

- **Bronkenmerk**: het gebruikerskenmerk uit het bronsysteem (zoals: Azure Active Directory).
- **Doelkenmerk**: het gebruikerskenmerk in het doelsysteem (zoals: ServiceNow).
- **Standaardwaarde indien null (optioneel)** : de waarde die wordt doorgegeven aan het doelsysteem als het bronkenmerk null is. Deze waarde wordt alleen ingericht bij het maken van een gebruiker. 'Standaardwaarde indien null' wordt niet ingericht bij het bijwerken van een bestaande gebruiker. Als u bijvoorbeeld alle bestaande gebruikers in het doelsysteem wilt inrichten met een bepaalde functie (en deze null is in het bronsysteem), kunt u de volgende [expressie](../app-provisioning/functions-for-customizing-application-data.md) gebruiken: Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Zorg ervoor dat u DefaultValue vervangt door de waarde die u wilt inrichten als deze null is in het bronsysteem. 
- **Objecten koppelen met dit kenmerk**: geeft aan of deze toewijzing moet worden gebruikt om gebruikers uniek te identificeren tussen het bron- en doelsysteem. Meestal wordt dit ingesteld op de userPrincipalName of het kenmerk mail in Azure AD, dat doorgaans is gekoppeld aan een gebruikersnaamveld in een doeltoepassing.
- **Prioriteit bij koppelen**: er kunnen meerdere overeenkomende kenmerken worden ingesteld. Als dat het geval is, worden deze geëvalueerd in de volgorde die hier is gedefinieerd. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd. Hoewel u zo veel overeenkomende kenmerken kunt instellen als u wilt, is het belangrijk om te kijken of de kenmerken die u als overeenkomende kenmerken gebruikt, echt uniek zijn en overeenkomende kenmerken moeten zijn. Over het algemeen hebben klanten 1 of 2 overeenkomende kenmerken in hun configuratie. 
- **Deze toewijzing toepassen**
  - **Altijd**: deze toewijzing toepassen bij het maken en bijwerken van gebruikers.
  - **Alleen tijdens het maken van een object**: deze toewijzing alleen toepassen bij het maken van gebruikers.

## <a name="matching-users-in-the-source-and-target--systems"></a>Overeenkomende gebruikers in de bron- en doelsystemen
De inrichtingsservice van Azure AD kan worden geïmplementeerd in zowel 'greenfield'-scenario's (waarbij gebruikers niet bestaan in het doelsysteem) als 'brownfield'-scenario's (waarbij gebruikers al bestaan in het doelsysteem). Om beide scenario's te ondersteunen, hanteert de inrichtingsservice het concept van overeenkomende kenmerken. Overeenkomende kenmerken maken het mogelijk om te bepalen hoe een gebruiker in het bronsysteem uniek wordt geïdentificeerd en wordt gekoppeld aan de gebruiker in het doelsysteem. Als onderdeel van het plannen van uw implementatie, identificeert u het kenmerk dat kan worden gebruikt om een gebruiker uniek te identificeren in de bron- en doelsystemen. Aandachtspunten:

- **Overeenkomende kenmerken moeten uniek zijn:** Klanten gebruiken vaak kenmerken als userPrincipalName, mail of objectID als het overeenkomende kenmerk.
- **U kunt meerdere kenmerken gebruiken als overeenkomende kenmerken:** U kunt meerdere kenmerken definiëren die moeten worden geëvalueerd bij het koppelen van gebruikers, evenals de volgorde waarin ze worden geëvalueerd (gedefinieerd via Prioriteit bij koppelen in de gebruikersinterface). Als u bijvoorbeeld drie kenmerken definieert als overeenkomende kenmerken en een gebruiker uniek is gekoppeld na het evalueren van de eerste twee kenmerken, wordt het derde kenmerk niet meer geëvalueerd door de service. De service evalueert overeenkomende kenmerken in de opgegeven volgorde en stopt met de evaluatie zodra er een overeenkomst is gevonden.  
- **De waarde in het bron- en doelsysteem hoeven niet exact overeen te komen:** De waarde in het doelsysteem kan een eenvoudige functie zijn van de waarde in het bronsysteem. Dit betekent dat de ene waarde kan bestaan uit het kenmerk emailAddress in de bron en userPrincipalName in het doel, waarna ze worden gekoppeld via een functie van het kenmerk emailAddress waarmee een aantal tekens wordt vervangen door de waarde van een constante.  
- **Koppeling op basis van een combinatie van kenmerken wordt niet ondersteund:** De meeste toepassingen bieden geen ondersteuning voor query's op basis van twee eigenschappen. Daarom is het niet mogelijk om tot een overeenkomst te komen op basis van een combinatie van kenmerken. Het is wel mogelijk om afzonderlijke eigenschappen na elkaar te evalueren.
- **Alle gebruikers moeten een waarde hebben voor ten minste één overeenkomend kenmerk:** Als u één overeenkomend kenmerk definieert, moeten alle gebruikers een waarde voor dat kenmerk hebben in het bronsysteem. Als u bijvoorbeeld userPrincipalName definieert als het overeenkomende kenmerk, moeten alle gebruikers een userPrincipalName hebben. Als u meerdere overeenkomende kenmerken definieert (bijvoorbeeld extensionAttribute1 en mail), hoeven niet alle gebruikers hetzelfde overeenkomende kenmerk te hebben. De ene gebruiker kan wel het kenmerk extensionAttribute1 hebben maar niet mail, terwijl een andere gebruiker het kenmerk mail heeft maar niet extensionAttribute1. 
- **De doeltoepassing moet filteren op het overeenkomende kenmerk ondersteunen:** Toepassingsontwikkelaars kunnen filteren op een subset van kenmerken toestaan in hun gebruikers- of groeps-API. Voor toepassingen in de galerie zorgen we ervoor dat de toewijzing van het standaardkenmerk wordt uitgevoerd voor een kenmerk waarop kan worden gefilterd door de API van de doeltoepassing. Wanneer u het standaard overeenkomende kenmerk wijzigt voor de doeltoepassing, raadpleegt u de documentatie van de API van de doeltoepassing om te controleren of er op het kenmerk kan worden gefilterd.  

## <a name="editing-group-attribute-mappings"></a>Toewijzingen van groepskenmerken bewerken

Een beperkt aantal toepassingen, zoals ServiceNow, Box en G suite, ondersteunt de mogelijkheid van het inrichten van groepsobjecten en gebruikersobjecten. Groepsobjecten kunnen groepseigenschappen bevatten, zoals weergavenamen en e-mailaliassen, naast groepsleden.

![Voorbeeld van ServiceNow met ingerichte groeps- en gebruikersobjecten](./media/customize-application-attributes/24.png)

Het inrichten van groepen kan desgewenst worden in- of uitgeschakeld door de groepstoewijzing te selecteren onder **Toewijzingen** en **Ingeschakeld** in te stellen op de gewenste optie in het scherm **Kenmerktoewijzing**.

De kenmerken die als onderdeel van groepsobjecten worden ingericht, kunnen op dezelfde manier worden aangepast als gebruikersobjecten, zoals eerder is beschreven. 

> [!TIP]
> Het inrichten van groepsobjecten (eigenschappen en leden) is een ander concept dan het [toewijzen van groepen](../manage-apps/assign-user-or-group-access-portal.md) aan een toepassing. Het is mogelijk om een groep toe te wijzen aan een toepassing en alleen de gebruikersobjecten in te richten die deel uitmaken van de groep. Het is niet vereist om groepsobjecten volledig in te richten als u groepen wilt gebruiken in toewijzingen.

## <a name="editing-the-list-of-supported-attributes"></a>De lijst met ondersteunde kenmerken bewerken

De gebruikerskenmerken die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd. De meeste API's voor gebruikersbeheer van toepassingen bieden geen ondersteuning voor schemadetectie. De inrichtingsservice van Azure AD kan de lijst met ondersteunde kenmerken dus niet dynamisch genereren door het aanroepen van de toepassing.

Sommige toepassingen ondersteunen echter aangepaste kenmerken en de inrichtingsservice van Azure AD kan wel lezen en schrijven naar aangepaste kenmerken. Als u definities van kenmerken wilt invoeren in de Azure-portal, schakelt u het selectievakje **Geavanceerde opties weergeven** onderaan het scherm **Kenmerktoewijzing** in en selecteert u vervolgens **Kenmerkenlijst bewerken voor** uw app.

Enkele voorbeelden van toepassingen en systemen die ondersteuning bieden voor aanpassing van de lijst met kenmerken:

- SalesForce
- ServiceNow
- Workday naar Active Directory / Workday naar Azure Active Directory
- SuccessFactors naar Active Directory / SuccessFactors naar Azure Active Directory
- Azure Active Directory ([standaardkenmerken van Azure AD Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) en aangepaste directory-extensies worden ondersteund)
- Apps die ondersteuning bieden voor [SCIM 2.0](https://tools.ietf.org/html/rfc7643)
- Voor write-back van Azure Active Directory naar Workday of SuccessFactors wordt dit ondersteund om relevante metagegevens bij te werken voor ondersteunde kenmerken (XPATH en JSONPath). Aanpassing van de lijst met kenmerken wordt echter niet ondersteund voor het toevoegen van nieuwe kenmerken van Workday of SuccessFactors, afgezien van de kenmerken die zijn opgenomen in het standaardschema


> [!NOTE]
> Het bewerken van de lijst met ondersteunde kenmerken wordt alleen aanbevolen voor beheerders die het schema van hun toepassingen en systemen hebben aangepast, en die weten hoe hun aangepaste kenmerken zijn gedefinieerd. Hiervoor kan het nodig zijn om ervaring te hebben met de API's en ontwikkeltools van een toepassing of systeem. De mogelijkheid om de lijst met ondersteunde kenmerken te bewerken is standaard vergrendeld, maar klanten kunnen deze mogelijkheid inschakelen door naar de volgende URL te gaan: https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true. U kunt vervolgens naar uw toepassing navigeren om de kenmerkenlijst weer te geven, zoals [hierboven](#editing-the-list-of-supported-attributes) wordt beschreven. 

Bij het bewerken van de lijst met ondersteunde kenmerken zijn de volgende eigenschappen beschikbaar:

- **Naam**: de systeemnaam van het kenmerk, zoals gedefinieerd in het schema van het doelobject.
- **Typ**: het type gegevens dat in het kenmerk wordt opgeslagen, zoals gedefinieerd in het schema van het doelobject. De volgende typen zijn mogelijk:
  - *Binair*: het kenmerk bevat binaire gegevens.
  - *Booleaans*: het kenmerk bevat een waarde True of False.
  - *DatumTijd*: het kenmerk bevat een datumtekenreeks.
  - *Geheel getal*: het kenmerk bevat een geheel getal.
  - *Verwijzing*: het kenmerk bevat een id die verwijst naar een waarde die is opgeslagen in een andere tabel in de doeltoepassing.
  - *Tekenreeks*: het kenmerk bevat een tekenreeks.
- **Primaire sleutel?** : Of het kenmerk is gedefinieerd als een primaire-sleutelveld in het schema van het doelobject.
- **Vereist?** : Of het kenmerk moet worden ingevuld in de doeltoepassing of het doelsysteem.
- **Meerdere waarden?** : Of het kenmerk meerdere waarden ondersteunt.
- **Hoofdlettergevoelig?** : Of bij het evalueren van de kenmerkwaarden onderscheid wordt gemaakt tussen hoofdletters en kleine letters.
- **API-expressie**: Alleen gebruiken als dit wordt vermeld in de documentatie voor een specifieke inrichtingsconnector (zoals Workday).
- **Objectkenmerk waarnaar wordt verwezen**: als het een kenmerk van het type Verwijzing is, kunt u in dit menu de tabel en het kenmerk in de doeltoepassing selecteren die de waarde bevat die aan het kenmerk is gekoppeld. Als u bijvoorbeeld een kenmerk met de naam 'Afdeling' hebt waarvan de opgeslagen waarde verwijst naar een object in een afzonderlijke tabel 'Afdelingen', selecteert u 'Afdelingen.Naam'. De verwijzingstabellen en de primaire-id-velden die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd en kunnen momenteel niet worden bewerkt via de Azure-portal. U kunt ze wel bewerken met behulp van de [Microsoft Graph API](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Een aangepast uitbreidingskenmerk inrichten voor een SCIM-compatibele toepassing
In de SCIM-RFC is een basisschema voor gebruikers en groepen gedefinieerd, maar de norm biedt ook ruimte voor uitbreidingen van het schema om te voldoen aan de behoeften van uw toepassing. Een aangepast kenmerk toevoegen aan een SCIM-toepassing:
   1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com) en selecteer achtereenvolgens **Bedrijfstoepassingen**, uw toepassing en **Inrichten**.
   2. Selecteer onder **Toewijzingen** het object (gebruiker of groep) waarvoor u een aangepast kenmerk wilt toevoegen.
   3. U kunt ook onderaan de pagina **Geavanceerde opties weergeven** selecteren.
   4. Selecteer **Kenmerkenlijst bewerken voor <naam van app>** .
   5. Voer in de velden onderaan de lijst met kenmerken gegevens in voor het aangepaste kenmerk. Selecteer ten slotte **Kenmerk toevoegen**.

Voor SCIM-toepassingen moet de naam van het kenmerk voldoen aan het patroon dat in het voorbeeld hieronder wordt weergegeven. CustomExtensionName en CustomAttribute kunnen worden aangepast volgens de vereisten van uw toepassing, bijvoorbeeld: urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 

Deze instructies gelden alleen voor SCIM-compatibele toepassingen. Toepassingen zoals ServiceNow en Salesforce zijn niet geïntegreerd met Azure AD via SCIM. Deze specifieke naamruimte is dan ook niet vereist voor deze apps als u een aangepast kenmerk gaat toevoegen.

Aangepaste kenmerken kunnen geen referentiële kenmerken zijn, en evenmin kenmerken met meerdere waarden of van een complex type. Aangepaste uitbreidingskenmerken met meerdere waarden en van een complex type worden momenteel alleen ondersteund voor toepassingen in de galerie.  
 
**Voorbeeld van een gebruiker met een uitbreidingskenmerk:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Een rol inrichten bij een SCIM-app
Volg de onderstaande stappen om rollen voor een gebruiker in te richten bij uw toepassing. De onderstaande beschrijving is specifiek bedoeld voor aangepaste SCIM-toepassingen. Gebruik voor galerie toepassingen zoals Salesforce en ServiceNow de vooraf gedefinieerde roltoewijzingen. In de onderstaande lijst wordt beschreven hoe u het kenmerk AppRoleAssignments transformeert naar de indeling die door uw toepassing wordt verwacht.

- Als u een appRoleAssignment in Azure AD wilt toewijzen aan een rol in uw toepassing, moet u het kenmerk transformeren met behulp van een [expressie](../app-provisioning/functions-for-customizing-application-data.md). Het kenmerk appRoleAssignment **mag niet rechtstreeks worden toegewezen** aan een rolkenmerk zonder een expressie te gebruiken om de rolgegevens te parseren. 

- **SingleAppRoleAssignment** 
  - **Wanneer gebruiken**: Gebruik de expressie SingleAppRoleAssignment om één rol voor een gebruiker in te richten en de primaire functie op te geven. 
  - **Hoe configureren:** Volg de stappen die hierboven worden beschreven om naar de pagina Kenmerktoewijzingen te gaan en gebruik de expressie SingleAppRoleAssignment voor toewijzing aan het kenmerk roles. Er zijn drie roles-kenmerken waaruit u kunt kiezen: (roles[primary eq "True"].display, roles[primary eq "True].type en roles[primary eq "True"].value). U kunt ervoor kiezen om bepaalde of alle role-kenmerken in uw toewijzingen op te nemen. Als u er meer dan één wilt gebruiken, voegt u gewoon een nieuwe toewijzing toe en neemt u deze op als het doelkenmerk.  
  
  ![SingleAppRoleAssignment toevoegen](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Aandachtspunten**
    - Zorg ervoor dat er niet meerdere rollen aan een gebruiker zijn toegewezen. We kunnen dan namelijk niet garanderen welke rol zal worden ingericht.
    
  - **Voorbeeldaanvraag (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Voorbeelduitvoer (PATCH)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
De aanvragen voor PATCH en POST hebben een verschillende indeling. Om ervoor te zorgen dat POST en PATCH in dezelfde indeling worden verzonden, kunt u de functievlag gebruiken die [hier](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior) wordt beschreven. 

- **AppRoleAssignmentsComplex** 
  - **Wanneer gebruiken**: Gebruik de expressie AppRoleAssignmentsComplex om meerdere rollen in te richten voor een gebruiker. 
  - **Hoe configureren:** Bewerk de lijst met ondersteunde kenmerken zoals hierboven wordt beschreven om een nieuw kenmerk voor rollen op te nemen: 
  
    ![Functies toevoegen](./media/customize-application-attributes/add-roles.png)<br>

    Gebruik vervolgens de expressie AppRoleAssignmentsComplex voor toewijzing aan het aangepaste role-kenmerk, zoals wordt weergegeven in de onderstaande afbeelding:

    ![AppRoleAssignmentsComplex toevoegen](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aandachtspunten**
    - Alle rollen worden ingericht als primary = false.
    - De POST bevat het type rol. De PATCH-aanvraag bevat geen type. Er wordt aan gewerkt om het type te verzenden in zowel POST- als PATCH-aanvragen.
    
  - **Voorbeelduitvoer** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Een kenmerk met meerdere waarden inrichten
Bepaalde kenmerken, zoals phoneNumbers en emails, zijn kenmerken met meerdere waarden waarbij u mogelijk verschillende typen telefoonnummers of e-mailadressen moet opgeven. Gebruik de onderstaande expressie voor kenmerken met meerdere waarden. Hiermee kunt u het type kenmerk opgeven en dat toewijzen aan het overeenkomende Azure AD-gebruikerskenmerk voor de waarde. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>De standaardkenmerken en standaardkenmerktoewijzingen herstellen

Als u helemaal opnieuw moet beginnen en de instellingen van de bestaande toewijzingen wilt resetten, schakelt u het selectievakje **Standaardtoewijzingen herstellen** in en slaat u de configuratie op. Hiermee worden alle toewijzingen en bereikfilters teruggezet op de instellingen die van kracht zijn op het moment dat een toepassing vanuit de toepassingsgalerie wordt toegevoegd aan uw Azure AD-tenant.

Als u deze optie selecteert, wordt er in feite een hersynchronisatie van alle gebruikers geforceerd terwijl de inrichtingsservice wordt uitgevoerd.

> [!IMPORTANT]
> We raden u ten zeerste aan **Inrichtingsstatus** op **Uit** te zetten voordat u deze optie uitvoert.

## <a name="what-you-should-know"></a>Wat u moet weten

- Microsoft Azure AD biedt een efficiënte implementatie van een synchronisatieproces. In een geïnitialiseerde omgeving worden alleen objecten waarvoor updates vereist zijn, verwerkt tijdens een synchronisatiecyclus.
- Het bijwerken van kenmerktoewijzingen heeft gevolgen voor de prestaties van een synchronisatiecyclus. Om de configuratie van een kenmerktoewijzings bij te werken, moeten alle beheerde objecten opnieuw worden geëvalueerd.
- Een aanbevolen best practice is om het aantal opeenvolgende wijzigingen in uw kenmerktoewijzingen daarom tot een minimum te beperken.
- Het toevoegen van een fotokenmerk voor inrichting bij een app wordt momenteel niet ondersteund. De reden hiervoor is dat u geen formaat kunt opgeven voor het synchroniseren van de foto. U kunt deze functie aanvragen op [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory).
- Het kenmerk IsSoftDeleted maakt vaak deel uit van de standaardtoewijzingen voor een toepassing. IsSoftdeleted kan waar zijn in een van de vier scenario's (de gebruiker valt buiten het bereik omdat hij of zij niet meer is toegewezen aan de toepassing, de gebruiker valt buiten het bereik omdat hij of zij niet voldoet aan een bereikfilter, de gebruiker is tijdelijk verwijderd in Azure AD of de eigenschap AccountEnabled is ingesteld op False voor de gebruiker). Het wordt afgeraden om het kenmerk IsSoftDeleted te verwijderen uit de kenmerktoewijzingen.
- De inrichtingsservice van Azure AD biedt geen ondersteuning voor het inrichten van null-waarden.
- De primaire sleutel, meestal 'ID', mag niet als doelkenmerk worden opgenomen in uw kenmerktoewijzingen. 
- Het kenmerk role moet meestal worden toegewezen met behulp van een expressie, in plaats van een directe toewijzing. Zie de paragraaf hierboven voor meer informatie over de toewijzing van rollen. 
- Hoewel u groepen kunt uitschakelen in toewijzingen, wordt het uitschakelen van gebruikers niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

- [Automatisch gebruikers voor SaaS-apps inrichten en de inrichting ongedaan maken](user-provisioning.md)
- [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
- [Bereikfilters voor het inrichten van gebruikers](define-conditional-rules-for-provisioning-user-accounts.md)
- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
- [Lijst met zelfstudies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)