---
title: Verificatie toevoegen voor het beveiligen van aanroepen naar aangepaste Api's
description: Verificatie instellen voor betere beveiliging van aanroepen naar aangepaste Api's van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993173"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Verbeter de beveiliging van aanroepen naar aangepaste Api's van Azure Logic Apps

Als u de beveiliging voor het aanroepen van uw Api's wilt verbeteren, kunt u Azure Active Directory (Azure AD)-verificatie instellen via de Azure Portal zodat u de code niet hoeft bij te werken. Of u kunt verificatie vereisen en afdwingen via de code van uw API.

## <a name="authentication-options-for-your-api"></a>Verificatie opties voor uw API

U kunt de beveiliging voor het aanroepen van uw aangepaste API op de volgende manieren verbeteren:

* [Er zijn geen code wijzigingen](#no-code): Bescherm uw api met [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) via de Azure Portal, zodat u uw code niet hoeft bij te werken of uw API opnieuw moet implementeren.

  > [!NOTE]
  > De Azure AD-verificatie die u inschakelt in de Azure Portal biedt standaard geen verfijnde autorisatie. Met deze verificatie wordt uw API bijvoorbeeld vergrendeld op alleen een specifieke Tenant, niet op een specifieke gebruiker of app. 

* [De code van uw API bijwerken](#update-code): Beveilig uw API door [certificaat verificatie](#certificate), [basis verificatie](#basic)of [Azure AD-verificatie](#azure-ad-code) af te dwingen via code.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Aanroepen naar uw API verifiëren zonder code te wijzigen

Hier volgen de algemene stappen voor deze methode:

1. Maak twee toepassings identiteiten voor Azure Active Directory (Azure AD): één voor uw logische app en een voor uw web-app (of API-app).

2. Als u aanroepen naar uw API wilt verifiëren, gebruikt u de referenties (client-ID en geheim) voor de service-principal die is gekoppeld aan de Azure AD-toepassings-id voor uw logische app.

3. Neem de toepassings-Id's op in de definitie van de logische app.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Deel 1: een Azure AD-toepassings-id maken voor uw logische app

Uw logische app maakt gebruik van deze Azure AD-toepassings identiteit om te verifiëren bij Azure AD. U hoeft deze identiteit slechts één keer in te stellen voor uw Directory. U kunt er bijvoorbeeld voor kiezen om dezelfde identiteit voor al uw Logic apps te gebruiken, ook al kunt u unieke identiteiten voor elke logische app maken. U kunt deze identiteiten instellen in de Azure Portal of [Power shell](#powershell)gebruiken.

**Maak de toepassings-id voor uw logische app in de Azure Portal**

1. Kies in het [Azure Portal](https://portal.azure.com "https://portal.azure.com") **Azure Active Directory**. 

2. Controleer of u zich in dezelfde map bevindt als de web-app of API-app.

   > [!TIP]
   > Als u wilt scha kelen tussen directory's, kiest u uw profiel en selecteert u een andere map. Of kies **overzicht**  >  **Switch Directory**.

3. Kies in het menu Directory onder **beheren** de optie **app-registraties**  >  **nieuwe toepassing registreren**.

   > [!TIP]
   > De lijst met app-registraties bevat standaard alle app-registraties in uw Directory. Als u alleen uw app-registraties wilt weer geven, gaat u naar het zoekvak en selecteert u **mijn apps**. 

   ![Nieuwe app-registratie maken](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Geef de identiteit van uw toepassing een naam, laat het **toepassings type** ingesteld op **Web app/API**, geef een unieke teken reeks op die is opgemaakt als een domein voor de **aanmeldings-URL** en kies **maken**.

   ![Geef een naam en aanmeldings-URL op voor de toepassings-id](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   De toepassings-id die u hebt gemaakt voor uw logische app wordt nu weer gegeven in de lijst app-registraties.

   ![Toepassings-id voor uw logische app](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Selecteer uw nieuwe toepassings identiteit in de lijst app-registraties. Kopieer de **toepassings-id** en sla deze op om te gebruiken als de ' client-id ' voor uw logische app in deel 3.

   ![De toepassings-ID voor de logische app kopiëren en opslaan](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Als uw toepassings-id-instellingen niet worden weer gegeven, kiest u **instellingen** of **alle instellingen**.

7. Kies **sleutels** onder **API-toegang**. Geef onder **Beschrijving** een naam op voor de sleutel. Selecteer onder **verloopt** een duur voor de sleutel.

   De sleutel die u maakt, fungeert als het geheim of het wacht woord van de toepassings-id voor uw logische app.

   ![Sleutel maken voor de logische app-identiteit](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Kies **Opslaan** op de werk balk. Onder **waarde** wordt uw sleutel nu weer gegeven. 
**Zorg ervoor dat u de sleutel kopieert en opslaat** voor later gebruik omdat de sleutel verborgen is wanneer u de pagina **sleutels** verlaat.

   Wanneer u uw logische app in deel 3 configureert, geeft u deze sleutel op als ' geheim ' of wacht woord.

   ![Kopieer en bewaar de sleutel voor later](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**De toepassings-id voor uw logische app maken in Power shell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt deze taak uitvoeren via Azure Resource Manager met Power shell. Voer in Power shell de volgende opdrachten uit:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Voer een wacht woord in en druk op ENTER.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Zorg ervoor dat u de **Tenant-id** (GUID voor uw Azure AD-Tenant), de **toepassings-id** en het wacht woord die u hebt gebruikt, kopieert.

Meer informatie over het [maken van een service-principal met Power shell voor toegang tot resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Deel 2: een Azure AD-toepassings-id maken voor uw web-app of API-app

Als uw web-app of API-app al is geïmplementeerd, kunt u verificatie inschakelen en de toepassings-id maken in de Azure Portal. Anders kunt u [verificatie inschakelen wanneer u implementeert met een Azure Resource Manager sjabloon](#authen-deploy). 

**De toepassings-id maken en verificatie inschakelen in de Azure Portal voor geïmplementeerde apps**

1. Zoek en selecteer uw web-app of API-app in de [Azure Portal](https://portal.azure.com "https://portal.azure.com"). 

2. Kies onder **instellingen** de optie **verificatie/autorisatie**. Schakel onder **app service verificatie** de optie verificatie **in**. Kies **Azure Active Directory** bij **verificatie providers**.

   ![Verificatie inschakelen](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Maak nu een toepassings-id voor uw web-app of API-app, zoals hier wordt weer gegeven. Stel op de pagina **instellingen Azure Active Directory** de **beheer modus** in op **Express**. Kies **nieuwe AD-app maken**. Geef de identiteit van uw toepassing een naam en kies **OK**. 

   ![Een toepassings-id voor uw web-app of API-app maken](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Kies **Opslaan** op de pagina **Verificatie/autorisatie**.

Nu moet u de client-ID en Tenant-ID vinden voor de toepassings identiteit die is gekoppeld aan uw web-app of API-app. U gebruikt deze Id's in deel 3. Ga dus door met deze stappen voor de Azure Portal.

**De client-ID en Tenant-ID van de toepassings identiteit zoeken voor uw web-app of API-app in de Azure Portal**

1. Kies **Azure Active Directory** bij **verificatie providers**. 

   ![Azure Active Directory kiezen](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Stel op de pagina **instellingen Azure Active Directory** de **beheer modus** in op **Geavanceerd**.

3. Kopieer de **client-id** en sla de GUID op voor gebruik in deel 3.

   > [!TIP] 
   > Als de URL van de **client-id** en de **certificaat verlener** niet worden weer gegeven, vernieuwt u de Azure Portal en herhaalt u stap 1.

4. Kopieer en sla alleen de GUID voor deel 3 op onder URL van de **Uitgever**. U kunt deze GUID ook gebruiken in de implementatie sjabloon van uw web-app of API-app, indien nodig.

   Deze GUID is de GUID van uw specifieke Tenant (Tenant-ID) en moet in deze URL worden weer gegeven: `https://sts.windows.net/{GUID}`

5. Sluit de pagina met **Azure Active Directory instellingen** zonder uw wijzigingen op te slaan.

<a name="authen-deploy"></a>

**Verificatie inschakelen wanneer u met een Azure Resource Manager-sjabloon implementeert**

U moet nog steeds een Azure AD-toepassings-id voor uw web-app of API-app maken die afwijkt van de app-identiteit voor uw logische app. Volg de vorige stappen in deel 2 van de Azure Portal om de toepassings-id te maken. 

U kunt ook de stappen in deel 1 volgen, maar zorg ervoor dat u de URL van uw web-app of API-app gebruikt `https://{URL}` voor **aanmeldings-** en **App-ID-URI**. In deze stappen moet u zowel de client-ID als de Tenant-ID opslaan voor gebruik in de implementatie sjabloon van uw app en ook voor deel 3.

> [!NOTE]
> Wanneer u de Azure AD-toepassings-id voor uw web-app of API-app maakt, moet u het Azure Portal gebruiken, niet Power shell. De Power shell-commandlet heeft niet de vereiste machtigingen ingesteld voor het aanmelden van gebruikers op een website.

Nadat u de client-ID en Tenant-ID hebt opgehaald, neemt u deze Id's op als een subresource van uw web-app of API-app in uw implementatie sjabloon:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Als u automatisch een lege web-app en een logische app wilt implementeren in combi natie met Azure Active Directory verificatie, [bekijkt u de volledige sjabloon hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)of klikt **u op implementeren naar Azure** :

[![Implementeren in Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Deel 3: de autorisatie sectie in uw logische app vullen

Deze autorisatie sectie is al ingesteld voor de vorige sjabloon, maar als u de logische app rechtstreeks wilt ontwerpen, moet u de sectie volledige autorisatie toevoegen.

Open de definitie van de logische app in de code weergave, ga naar de **http-** actie definitie, zoek het gedeelte **autorisatie** en voeg deze eigenschappen toe:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| tenant | Yes | De GUID voor de Azure AD-Tenant | 
| gericht | Yes | De GUID voor de doel resource waartoe u toegang wilt krijgen, wat de client-ID is van de toepassings identiteit voor uw web-app of API-app | 
| clientId | Yes | De GUID voor de client die toegang vraagt, de client-ID van de toepassings identiteit voor uw logische app | 
| geheim | Yes | De sleutel of het wacht woord van de toepassings-id voor de client die het toegangs token aanvraagt | 
| type | Yes | Het verificatie type. De waarde is voor ActiveDirectoryOAuth-verificatie `ActiveDirectoryOAuth` . | 
|||| 

Bijvoorbeeld:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Beveiligde API-aanroepen via code

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Verificatie via certificaat

Als u de binnenkomende aanvragen van uw logische app wilt valideren naar uw web-app of API-app, kunt u client certificaten gebruiken. Meer informatie over het instellen van uw code voor het [configureren van TLS-wederzijdse verificatie](../app-service/app-service-web-configure-tls-mutual-auth.md).

Neem de volgende eigenschappen op in de sectie **autorisatie** :

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| `type` | Ja | Het verificatie type. Voor TLS/SSL-client certificaten moet de waarde zijn `ClientCertificate` . |
| `password` | No | Het wacht woord voor toegang tot het client certificaat (PFX-bestand) |
| `pfx` | Yes | De met base64 gecodeerde inhoud van het client certificaat (PFX-bestand) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Basisverificatie

Als u binnenkomende aanvragen van uw logische app wilt valideren naar uw web-app of API-app, kunt u basis verificatie gebruiken, zoals een gebruikers naam en wacht woord. Basis verificatie is een algemeen patroon, en u kunt deze verificatie gebruiken in elke taal die wordt gebruikt om uw web-app of API-app te maken.

Neem de volgende eigenschappen op in de sectie **autorisatie** :

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| type | Yes | Het verificatie type dat u wilt gebruiken. Voor basis verificatie moet de waarde zijn `Basic` . | 
| gebruikersnaam | Yes | De gebruikers naam die u wilt gebruiken voor verificatie | 
| wachtwoord | Yes | Het wacht woord dat u wilt gebruiken voor verificatie | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Verificatie Azure Active Directory via code

De Azure AD-verificatie die u inschakelt in de Azure Portal biedt standaard geen verfijnde autorisatie. Met deze verificatie wordt uw API bijvoorbeeld vergrendeld op alleen een specifieke Tenant, niet op een specifieke gebruiker of app. 

Als u via code de API-toegang tot uw logische app wilt beperken, extraheert u de header met de JSON-webtoken (JWT). Controleer de identiteit van de aanroeper en weiger aanvragen die niet overeenkomen.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste Api's implementeren en aanroepen vanuit werk stromen voor logische apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
