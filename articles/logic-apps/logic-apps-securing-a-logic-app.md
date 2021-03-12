---
title: Toegang en gegevens beveiligen
description: Veilige toegang tot invoer, uitvoer, op aanvragen gebaseerde triggers, uitvoerings geschiedenis, beheer taken en toegang tot andere resources in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 7b082c226b38633d6c34ee2fe4d5227252b2bfcb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556380"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Beveiligde toegang en gegevens in Azure Logic Apps

Azure Logic Apps is afhankelijk van [Azure Storage](../storage/index.yml) om gegevens in rust op te slaan en automatisch te [versleutelen](../security/fundamentals/encryption-atrest.md). Deze versleuteling beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Azure Storage maakt standaard gebruik van door micro soft beheerde sleutels om uw gegevens te versleutelen. Zie [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie.

Als u de toegang wilt controleren en gevoelige gegevens in Azure Logic Apps wilt beveiligen, kunt u op de volgende gebieden extra beveiliging instellen:

* [Toegang tot inkomende oproepen voor activeringen op basis van een aanvraag](#secure-inbound-requests)
* [Toegang tot logische app-bewerkingen](#secure-operations)
* [Toegang tot de invoer en uitvoer van de uitvoerings geschiedenis](#secure-run-history)
* [Toegang tot parameter invoer](#secure-action-parameters)
* [Toegang tot uitgaande oproepen naar andere services en systemen](#secure-outbound-requests)
* [Maken van verbindingen voor specifieke connectors blok keren](#block-connections)
* [Isolatie richtlijnen voor Logic apps](#isolation-logic-apps)
* [Azure-beveiligings basislijn voor Azure Logic Apps](../logic-apps/security-baseline.md)

Zie de volgende onderwerpen voor meer informatie over beveiliging in Azure:

* [Overzicht van Azure-versleuteling](../security/fundamentals/encryption-overview.md)
* [Azure-gegevens versleuteling-at-rest](../security/fundamentals/encryption-atrest.md)
* [Azure Security-benchmark](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Toegang tot inkomende oproepen voor activeringen op basis van een aanvraag

Inkomende oproepen die een logische app via een op een aanvraag gebaseerde trigger ontvangt, zoals de trigger voor het [aanvragen](../connectors/connectors-native-reqres.md) of de [http-webhook](../connectors/connectors-native-webhook.md) , ondersteunen versleuteling en worden beveiligd met [Transport Layer Security (TLS) 1,2 (](https://en.wikipedia.org/wiki/Transport_Layer_Security)voorheen bekend als Secure Sockets Layer (SSL). Logic Apps afdwingt deze versie bij het ontvangen van een inkomende oproep naar de aanvraag trigger of een retour aanroep naar de trigger of actie van de HTTP-webhook. Als u TLS-Handshake-fouten ontvangt, moet u ervoor zorgen dat u TLS 1,2 gebruikt. Zie [het probleem met het TLS 1,0 oplossen](/security/solving-tls1-problem)voor meer informatie.

Binnenkomende oproepen ondersteunen deze coderings suites:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Hier vindt u meer manieren waarop u de toegang tot triggers kunt beperken die binnenkomende oproepen naar uw logische app ontvangen, zodat alleen geautoriseerde clients uw logische app kunnen aanroepen:

* [Hand tekeningen voor gedeelde toegang (SAS) genereren](#sas)
* [Verificatie Azure Active Directory openen inschakelen (Azure AD OAuth)](#enable-oauth)
* [Uw logische app beschikbaar maken met Azure API Management](#azure-api-management)
* [Inkomende IP-adressen beperken](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Hand tekeningen voor gedeelde toegang (SAS) genereren

Elk aanvraag eindpunt op een logische app heeft een [Shared Access Signature (SAS)](/rest/api/storageservices/constructing-a-service-sas) in de URL van het eind punt, die de volgende indeling heeft:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Elke URL bevat de `sp` `sv` para meter,, en `sig` query zoals beschreven in deze tabel:

| Queryparameter | Beschrijving |
|-----------------|-------------|
| `sp` | Hiermee geeft u de machtigingen op voor het gebruik van de toegestane HTTP-methoden. |
| `sv` | Hiermee geeft u de SAS-versie op die moet worden gebruikt voor het genereren van de hand tekening. |
| `sig` | Hiermee geeft u de hand tekening op die moet worden gebruikt voor het verifiëren van de toegang tot de trigger. Deze hand tekening wordt gegenereerd met behulp van de SHA256-algoritme met een geheime toegangs sleutel voor alle URL-paden en eigenschappen. Nooit beschikbaar of gepubliceerd, deze sleutel wordt versleuteld bewaard en opgeslagen met de logische app. Uw logische app machtigt alleen de triggers die een geldige hand tekening bevatten die is gemaakt met de geheime sleutel. |
|||

Inkomende aanroepen naar een aanvraag eindpunt kunnen slechts één autorisatie schema, ofwel SAS ofwel [Azure Active Directory open-verificatie](#enable-oauth)gebruiken. Hoewel het gebruik van één schema het andere schema niet uitschakelt, veroorzaakt het gebruik van beide schema's tegelijk een fout omdat de service niet weet welk schema u moet kiezen.

Zie de volgende secties in dit onderwerp voor meer informatie over het beveiligen van de toegang met SAS:

* [Toegangssleutels regenereren](#access-keys)
* [Url's voor verlopende call back maken](#expiring-urls)
* [Url's met een primaire of secundaire sleutel maken](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

Als u op elk gewenst moment een nieuwe beveiligings toegangs sleutel wilt genereren, gebruikt u de Azure REST API of Azure Portal. Alle eerder gegenereerde Url's die gebruikmaken van de oude sleutel, zijn ongeldig en hebben geen autorisatie meer om de logische app te activeren. De Url's die u na het opnieuw genereren hebt opgehaald, worden ondertekend met de nieuwe toegangs sleutel.

1. Open in de [Azure Portal](https://portal.azure.com)de logische app met de sleutel die u opnieuw wilt genereren.

1. Selecteer **toegangs sleutels** onder **instellingen** in het menu van de logische app.

1. Selecteer de sleutel die u opnieuw wilt genereren en voltooi het proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Url's voor verlopende call back maken

Als u de eind punt-URL voor een op aanvragen gebaseerde trigger met andere partijen deelt, kunt u Url's voor terugbellen genereren die specifieke sleutels gebruiken en verloop datums hebben. Op die manier kunt u sleutels naadloos draaien of de toegang beperken tot het activeren van uw logische app op basis van een specifieke tijds duur. Als u een verval datum voor een URL wilt opgeven, gebruikt u de [Logic Apps rest API](/rest/api/logic/workflowtriggers), bijvoorbeeld:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Neem in de hoofd tekst de `NotAfter` eigenschap op met behulp van een JSON-datum teken reeks. Deze eigenschap retourneert een call back-URL die alleen geldig is voor de `NotAfter` datum en tijd.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Url's met een primaire of secundaire sleutel maken

Wanneer u call back-Url's voor een op aanvragen gebaseerde trigger genereert of lijsteert, kunt u de sleutel opgeven die moet worden gebruikt voor het ondertekenen van de URL. Als u een URL wilt genereren die is ondertekend met een specifieke sleutel, gebruikt u de [Logic Apps rest API](/rest/api/logic/workflowtriggers), bijvoorbeeld:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

In de hoofd tekst, neemt `KeyType` u de eigenschap op als `Primary` of `Secondary` . Deze eigenschap retourneert een URL die is ondertekend door de opgegeven beveiligings sleutel.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Verificatie Azure Active Directory openen inschakelen (Azure AD OAuth)

Voor inkomende aanroepen naar een eind punt dat is gemaakt door een trigger die is gebaseerd op een aanvraag, kunt u [Azure AD OAuth](../active-directory/develop/index.yml) inschakelen door een autorisatie beleid te definiëren of toe te voegen voor uw logische app. Op deze manier gebruiken inkomende oproepen OAuth- [toegangs tokens](../active-directory/develop/access-tokens.md) voor verificatie.

Wanneer uw logische app een binnenkomende aanvraag met een OAuth-toegangs token ontvangt, vergelijkt de Azure Logic Apps-service de claims van het token met de claims die zijn opgegeven door elk autorisatie beleid. Als er een overeenkomst is tussen de claims van het token en alle claims in ten minste één beleid, is de autorisatie geslaagd voor de inkomende aanvraag. Het token kan meer claims hebben dan het aantal dat is opgegeven door het autorisatie beleid.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Overwegingen voordat u Azure AD OAuth inschakelt

* Een inkomende oproep naar het eind punt van de aanvraag kan slechts één autorisatie schema, ofwel Azure AD OAuth of [Shared Access Signature (SAS)](#sas), gebruiken. Hoewel het gebruik van één schema het andere schema niet uitschakelt, veroorzaakt het gebruik van beide schema's tegelijk een fout omdat de Logic Apps-service niet weet welk schema u moet kiezen.

* Alleen autorisatie schema's van [Bearer-type](../active-directory/develop/active-directory-v2-protocols.md#tokens) worden ondersteund voor Azure AD OAuth-toegangs tokens, wat betekent dat de `Authorization` header voor het toegangs token het `Bearer` type moet opgeven.

* Uw logische app is beperkt tot een maximum aantal autorisatie beleidsregels. Elk autorisatie beleid heeft ook een maximum aantal [claims](../active-directory/develop/developer-glossary.md#claim). Zie [Informatie over limieten en configuratie voor Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits) voor meer informatie.

* Een autorisatie beleid moet ten minste de **Issuer** claim bevatten, die een waarde heeft die begint met ofwel `https://sts.windows.net/` of `https://login.microsoftonline.com/` (OAuth v2) als de id van de Azure AD-Uitgever.

  Stel dat uw logische app een autorisatie beleid heeft waarvoor twee claim typen, **doel groep** en **Uitgever** vereist zijn. Deze sectie voor beeld van [nettolading](../active-directory/develop/access-tokens.md#payload-claims) voor een gedecodeerd toegangs token bevat beide claim typen waarbij `aud` de waarde van de **doel groep** is en `iss` de waarde van de **verlener** is:

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Azure AD OAuth inschakelen voor uw logische app

Volg deze stappen voor de Azure Portal-of Azure Resource Manager sjabloon:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Voeg in de [Azure Portal](https://portal.azure.com)een of meer autorisatie beleid toe aan uw logische app:

1. Zoek en open uw logische app in het [Azure Portal](https://portal.microsoft.com)in de ontwerp functie voor logische apps.

1. Selecteer in het menu van de logische app onder **instellingen** de optie **autorisatie**. Nadat het autorisatie venster is geopend, selecteert u **beleid toevoegen**.

   ![Selecteer autorisatie > beleid toevoegen](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Geef informatie op over het autorisatie beleid door de [claim typen](../active-directory/develop/developer-glossary.md#claim) en waarden op te geven die uw logische app verwacht in het toegangs token dat wordt gepresenteerd door elke inkomende oproep naar de aanvraag trigger:

   ![Informatie opgeven voor autorisatie beleid](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam van beleid** | Ja | De naam die u wilt gebruiken voor het autorisatie beleid |
   | **Claims** | Ja | De claim typen en-waarden die uw logische app accepteert van binnenkomende oproepen. De claim waarde is beperkt tot een [maximum aantal tekens](logic-apps-limits-and-config.md#authentication-limits). Dit zijn de beschik bare claim typen: <p><p>- **Verlener** <br>- **Gericht** <br>- **Onderwerp** <br>- **JWT-id** (JSON Web token-id) <p><p>De **claim** lijst moet mini maal de claim van de **verlener** bevatten, die een waarde heeft die begint met `https://sts.windows.net/` of `https://login.microsoftonline.com/` als de id van de Azure AD-Uitgever. Zie [claims in azure AD-beveiligings tokens](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)voor meer informatie over deze claim typen. U kunt ook uw eigen claim type en-waarde opgeven. |
   |||

1. Selecteer een van de volgende opties om een claim toe te voegen:

   * Als u een ander claim type wilt toevoegen, selecteert u **standaard claim toevoegen**, selecteert u het claim type en geeft u de claim waarde op.

   * Selecteer **aangepaste claim toevoegen** om uw eigen claim toe te voegen. Zie [optionele claims voor uw app bieden](../active-directory/develop/active-directory-optional-claims.md)voor meer informatie. Uw aangepaste claim wordt vervolgens opgeslagen als onderdeel van uw JWT-ID; bijvoorbeeld `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . 

1. Selecteer **beleid toevoegen** om een ander autorisatie beleid toe te voegen. Herhaal de vorige stappen om het beleid in te stellen.

1. Selecteer **Opslaan** als u klaar bent.

1. Als u de `Authorization` header wilt toevoegen uit het toegangs token in de trigger uitvoer op basis van een aanvraag, raadpleegt u de [header Authorization in de trigger uitvoer van aanvragen](#include-auth-header).


Werk stroom eigenschappen, zoals beleids regels, worden niet weer gegeven in de code weergave van de logische app in de Azure Portal. Voor toegang tot uw beleid via een programma kunt u de volgende API aanroepen via Azure Resource Manager (ARM): `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Zorg ervoor dat u de waarden van de tijdelijke aanduidingen vervangt door de ID van uw Azure-abonnement, de naam van de resource groep en de werk stroom.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

Definieer in uw ARM-sjabloon een autorisatie beleid volgens de volgende stappen en syntaxis:

1. Voeg in de `properties` sectie voor de [resource definitie van de logische app](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)een `accessControl` object toe als er geen bestaat, dat een `triggers` object bevat.

   Zie voor meer informatie over het `accessControl` object [binnenkomende IP-bereiken beperken in azure Resource Manager sjabloon](#restrict-inbound-ip-template) en [verwijzing naar sjablonen voor micro soft. Logic-werk stromen](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. Voeg in het `triggers` -object een `openAuthenticationPolicies` object toe dat het `policies` object bevat waarin u een of meer autorisatie beleidsregels definieert.

1. Geef een naam op voor het autorisatie beleid, stel het beleids type in op `AAD` en voeg een `claims` matrix toe waarin u een of meer claim typen opgeeft.

   De matrix moet mini maal `claims` het claim type van de verlener bevatten waarbij u de eigenschap van de claim instelt `name` op `iss` en instellen `value` op starten met `https://sts.windows.net/` of `https://login.microsoftonline.com/` als de id van de Azure AD-Uitgever. Zie [claims in azure AD-beveiligings tokens](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)voor meer informatie over deze claim typen. U kunt ook uw eigen claim type en-waarde opgeven.

1. Als u de `Authorization` header wilt toevoegen uit het toegangs token in de trigger uitvoer op basis van een aanvraag, raadpleegt u de [header Authorization in de trigger uitvoer van aanvragen](#include-auth-header).

Dit is de syntaxis die u moet volgen:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Kop ' Authorization ' insluiten in uitvoer van aanvragen trigger

Voor logische apps waarmee [Azure Active Directory open verificatie (Azure AD OAuth)](#enable-oauth) voor het autoriseren van binnenkomende oproepen voor toegang tot op aanvragen gebaseerde triggers wordt ingeschakeld, kunt u de trigger voor aanvragen of http-webhook activeren om de `Authorization` header uit het OAuth-toegangs token op te halen. Voeg in de onderliggende JSON-definitie van de trigger de eigenschap toe en stel deze in `operationOptions` op `IncludeAuthorizationHeadersInOutputs` . Hier volgt een voor beeld van de aanvraag trigger:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Schema verwijzing voor de trigger en actie typen-trigger voor aanvragen](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Schema verwijzing voor de trigger en actie typen-trigger voor HTTP-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Schema verwijzing voor de trigger-en actie typen-bewerkings opties](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Uw logische app beschikbaar maken met Azure API Management

Als u meer [verificatie protocollen](../active-directory/develop/authentication-vs-authorization.md) wilt toevoegen aan uw logische app, kunt u overwegen de [Azure API Management](../api-management/api-management-key-concepts.md) -service te gebruiken. Deze service helpt u uw logische app beschikbaar te maken als een API en biedt uitgebreide bewaking, beveiliging, beleid en documentatie voor elk eind punt. API Management kunt een openbaar of persoonlijk eind punt beschikbaar maken voor uw logische app. Als u toegang tot dit eind punt wilt verlenen, kunt u Azure AD OAuth, [client certificaten](#client-certificate-authentication)of andere beveiligings standaarden gebruiken om toegang tot het eind punt te verlenen. Wanneer API Management een aanvraag ontvangt, stuurt de service de aanvraag naar uw logische app, worden ook de nodige trans formaties of beperkingen door lopen. Om alleen API Management uw logische app aan te roepen, kunt u de [inkomende IP-adressen van uw logische app beperken](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Inkomende IP-adressen beperken

Samen met Shared Access Signature (SAS) kunt u de clients die uw logische app kunnen aanroepen, het beste beperken. Als u bijvoorbeeld het eind punt van de aanvraag beheert met behulp van [Azure API Management](../api-management/api-management-key-concepts.md), kunt u de logische app beperken tot het accepteren van aanvragen van het IP-adres voor het [API Management service-exemplaar dat u hebt gemaakt](../api-management/get-started-create-service-instance.md).

Ongeacht de IP-adressen die u opgeeft, kunt u nog steeds een logische app met een op aanvragen gebaseerde trigger uitvoeren met behulp van de [Logic Apps rest API: werk stroom triggers-aanvraag uitvoeren](/rest/api/logic/workflowtriggers/run) of door middel van API management. Voor dit scenario is echter nog steeds [verificatie](../active-directory/develop/authentication-vs-authorization.md) vereist voor de Azure-rest API. Alle gebeurtenissen worden weer gegeven in het controle logboek van Azure. Zorg ervoor dat u de beleids regels voor toegangs beheer dienovereenkomstig instelt.

Volg de volgende stappen voor de Azure Portal of uw Azure Resource Manager sjabloon om de inkomende IP-adressen voor uw logische app te beperken:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

In de [Azure Portal](https://portal.azure.com)beïnvloedt dit filter zowel triggers *als* acties, in tegens telling tot de beschrijving in de portal onder **toegestane binnenkomende IP-adressen**. Als u dit filter afzonderlijk wilt instellen voor triggers en voor acties, gebruikt u het `accessControl` object in een Azure Resource Manager sjabloon voor uw logische app of de [Logic apps rest API: werk stroom-maken of bijwerken](/rest/api/logic/workflows/createorupdate).

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen** onder **instellingen** in het menu van de logische app.

1. Kies in het gedeelte **toegangs beheer configuratie** onder **toegestane inkomende IP-adressen** het pad voor uw scenario:

   * Als u wilt dat uw logische app alleen kan worden opgeroepen als een geneste logische app met behulp van de ingebouwde [Azure Logic apps actie](../logic-apps/logic-apps-http-endpoint.md), selecteert u **alleen andere Logic apps**. dit werkt *alleen* wanneer u de **Azure Logic apps** actie gebruikt om de geneste logische app aan te roepen.

     Met deze optie wordt een lege matrix naar de logische app-resource geschreven en is vereist dat alleen aanroepen vanuit bovenliggende logische apps die gebruikmaken van de ingebouwde **Azure Logic apps** actie, de geneste logische app kunnen activeren.

   * Als u wilt dat uw logische app alleen kan worden opgeroepen als een geneste app met behulp van de HTTP-actie, selecteert u **specifieke IP-bereiken**, *niet* **alleen andere Logic apps**. Wanneer het vak **IP-bereiken voor triggers** wordt weer gegeven, voert u de [uitgaande IP-adressen](../logic-apps/logic-apps-limits-and-config.md#outbound)van de bovenliggende logische app in. Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x. x*. x.

     > [!NOTE]
     > Als u de **enige andere Logic apps** optie en de http-actie gebruikt om uw geneste logische app aan te roepen, wordt de aanroep geblokkeerd en krijgt u de fout melding ' 401 niet toegestaan '.

   * Voor scenario's waarbij u de inkomende aanroepen van andere IP-adressen wilt beperken, geeft u de IP-adresbereiken op die de trigger accepteert wanneer het vak **IP-bereiken voor triggers** wordt weer gegeven. Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x. x*. x.

1. U kunt, onder **aanroepen voor het ophalen van invoer-en uitvoer berichten van de uitvoerings geschiedenis naar de opgegeven IP-adressen beperken**, de IP-adresbereiken voor inkomende oproepen opgeven die toegang hebben tot invoer-en uitvoer berichten in de uitvoerings geschiedenis.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

Geef in uw ARM-sjabloon de toegestane binnenkomende IP-adresbereiken in de resource definitie van de logische app op met behulp van de `accessControl` sectie. In deze sectie gebruikt u de `triggers` , `actions` , en de optionele `contents` secties, afhankelijk van de `allowedCallerIpAddresses` sectie met de `addressRange` eigenschap en stelt u de waarde van de eigenschap in op het toegestane IP-adres bereik in *x.* x. x. x/x of *x. x. x. x-x,* x. x. x-indeling.

* Als uw geneste logische app gebruikmaakt van de **enige andere Logic apps** optie, waarmee binnenkomende oproepen alleen worden toegestaan vanuit andere logische apps die gebruikmaken van de ingebouwde Azure Logic apps actie, stelt `allowedCallerIpAddresses` u de eigenschap in op een lege matrix (**[]**) en *laat* u de `addressRange` eigenschap weg.

* Als uw geneste logische app gebruikmaakt van de **specifieke IP-bereiken** voor andere inkomende oproepen, zoals andere logische apps die gebruikmaken van de http-actie, neemt `allowedCallerIpAddresses` u de sectie op en stelt `addressRange` u de eigenschap in op het toegestane IP-bereik.

In dit voor beeld ziet u een resource definitie voor een geneste logische app waarmee inkomende oproepen alleen worden toegestaan vanuit Logic apps die gebruikmaken van de ingebouwde Azure Logic Apps actie:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

In dit voor beeld ziet u een resource definitie voor een geneste logische app die inkomende aanroepen toestaat vanuit Logic apps die gebruikmaken van de HTTP-actie:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Toegang tot logische app-bewerkingen

U kunt alleen specifieke gebruikers of groepen toestaan specifieke taken uit te voeren, zoals het beheren, bewerken en weer geven van logische apps. Als u de machtigingen wilt beheren, gebruikt u [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/role-assignments-portal.md) , zodat u aangepaste of ingebouwde rollen kunt toewijzen aan de leden van uw Azure-abonnement:

* [Inzender van Logic](../role-based-access-control/built-in-roles.md#logic-app-contributor)apps: Hiermee kunt u logische apps beheren, maar u kunt de toegang niet wijzigen.

* [Logische app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar u kunt ze niet bewerken of bijwerken.

U kunt [Azure resource Lock](../azure-resource-manager/management/lock-resources.md)gebruiken om te voor komen dat anderen uw logische app wijzigen of verwijderen. Deze mogelijkheid voor komt dat anderen productie resources wijzigen of verwijderen.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Toegang tot uitvoerings geschiedenis gegevens

Tijdens de uitvoering van een logische app worden alle gegevens [tijdens de overdracht versleuteld](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) met behulp van Transport Layer Security (TLS) en [in rust](../security/fundamentals/encryption-atrest.md). Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering bekijken, met inbegrip van de stappen die samen met de status, duur, invoer en uitvoer voor elke actie worden uitgevoerd. Deze uitgebreide details bieden inzicht in hoe uw logische app wordt uitgevoerd en waar u kunt beginnen met het oplossen van problemen die zich voordoen.

Wanneer u de uitvoerings geschiedenis van de logische app bekijkt, wordt uw toegang door Logic Apps geverifieerd en vindt u koppelingen naar de invoer en uitvoer voor de aanvragen en antwoorden voor elke uitvoering. Voor acties waarbij wacht woorden, geheimen, sleutels of andere gevoelige informatie worden verwerkt, wilt u echter voor komen dat anderen deze gegevens kunnen weer geven en gebruiken. Als uw logische app bijvoorbeeld een geheim krijgt van [Azure Key Vault](../key-vault/general/overview.md) om te gebruiken bij het verifiëren van een http-actie, wilt u dat geheim verbergen in de weer gave.

Als u de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app wilt beheren, hebt u de volgende opties:

* De [toegang beperken op basis van het IP-adres bereik](#restrict-ip).

  Met deze optie kunt u de toegang tot de uitvoerings geschiedenis beveiligen op basis van de aanvragen van een specifiek IP-adres bereik.

* [Gegevens in de uitvoerings geschiedenis beveiligen door gebruik te maken van een behulp van een betrouw](#obfuscate)bare

  In veel triggers en acties kunt u de invoer, uitvoer of beide beveiligen in de uitvoerings geschiedenis van een logische app.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Toegang beperken op basis van IP-adres bereik

U kunt de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app beperken zodat alleen aanvragen van specifieke IP-adresbereiken die gegevens kunnen weer geven.

Als u bijvoorbeeld wilt blok keren dat iedereen toegang heeft tot invoer en uitvoer, geeft u een IP-adres bereik op zoals `0.0.0.0-0.0.0.0` . Alleen gebruikers met beheerders machtigingen kunnen deze beperking verwijderen. Dit biedt de mogelijkheid om just-in-time toegang te krijgen tot de gegevens van uw logische app.

Voer de volgende stappen uit voor de Azure Portal of uw Azure Resource Manager sjabloon om de toegestane IP-bereiken op te geven:

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen** onder **instellingen** in het menu van de logische app.

1.   >  Selecteer **specifieke IP-bereiken** onder toegangs beheer configuratie **toegestane binnenkomende IP-adressen**.

1. Geef onder **IP-adresbereiken voor inhoud** de IP-adresbereiken op die toegang hebben tot inhoud van invoer en uitvoer.

   Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x.* x. x

#### <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

Geef in uw ARM-sjabloon de IP-bereiken op met behulp van de `accessControl` sectie `contents` in de resource definitie van de logische app, bijvoorbeeld:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Gegevens in de uitvoerings geschiedenis beveiligen met behulp van een afvallen

Veel triggers en acties hebben instellingen voor het beveiligen van invoer, uitvoer of beide vanuit de uitvoerings geschiedenis van een logische app. Voordat u deze instellingen kunt gebruiken om deze gegevens te beveiligen, raadpleegt u de volgende overwegingen:

* Wanneer u de invoer of uitvoer van een trigger of actie verduidelijkt, worden Logic Apps de beveiligde gegevens niet naar Azure Log Analytics verzonden. U kunt ook geen [bijgehouden eigenschappen](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) toevoegen aan deze trigger of actie voor bewaking.

* De [Logic apps-API voor het verwerken van de werk stroom geschiedenis](/rest/api/logic/) retourneert geen beveiligde uitvoer.

* Als u uitvoer wilt beveiligen op basis van een actie die invoer verduidelijkt of als deze expliciet worden uitgevoerd, schakelt u de **beveiligde uitvoer** in die actie hand matig in.

* Zorg ervoor dat u **beveiligde invoer** of **beveiligde uitvoer** inschakelt in downstream-acties waarbij u verwacht dat de uitvoerings geschiedenis deze gegevens verduidelijkt.

  **Instelling voor beveiligde uitvoer**

  Wanneer u **beveiligde uitvoer** in een trigger of actie hand matig inschakelt, Logic apps deze uitvoer in de uitvoerings geschiedenis verbergen. Als een stroomafwaartse actie deze beveiligde uitvoer expliciet gebruikt als invoer, Logic Apps de invoer van deze actie in de uitvoerings geschiedenis verbergen, maar wordt de instelling **beveiligde invoer** van de actie *niet ingeschakeld* .

  ![Beveiligde uitvoer als invoer en stroomafwaartse impact op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  De acties opstellen, parseren JSON en Response hebben alleen de instelling **beveiligde invoer** . Wanneer deze optie is ingeschakeld, worden de uitvoer van deze acties ook verborgen. Als deze acties expliciet gebruikmaken van de upstream beveiligde uitvoer als invoer, worden de invoer en uitvoer van deze acties door Logic Apps verborgen, maar worden de instelling **beveiligde invoer waarden** *niet ingeschakeld* . Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde uitvoer als invoer met downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Instelling voor beveiligde invoer**

  Wanneer u **beveiligde invoer** in een trigger of actie hand matig inschakelt, Logic apps deze invoer in de uitvoerings geschiedenis verbergen. Als een stroomafwaartse actie expliciet gebruikmaakt van de zicht bare uitvoer van die trigger of actie als invoer, Logic Apps de invoer van deze stroomafwaartse actie verbergen in de uitvoerings geschiedenis, maar worden *geen* **beveiligde invoer** in deze actie ingeschakeld en worden de uitvoer van deze actie niet verborgen.

  ![Beveiligde invoer en downstream van invloed op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Als de acties opstellen, parseren van JSON en respons expliciet gebruikmaken van de zicht bare uitvoer van de trigger of actie die de beveiligde invoer heeft, Logic Apps de invoer en uitvoer van deze acties verbergen, maar schakelt de instelling **beveiligde invoer** van deze actie *niet in* . Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde invoer en downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Invoer en uitvoer in de ontwerp functie beveiligen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

   ![Logische app openen in de ontwerp functie voor logische apps](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Selecteer de knop met weglatings tekens (**...**) op de trigger of de actie waar u gevoelige gegevens wilt beveiligen en selecteer vervolgens **instellingen**.

   ![Trigger-of actie-instellingen openen](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Schakel **beveiligde invoer**, **beveiligde uitvoer** of beide in. Wanneer u klaar bent, selecteert u **Gereed**.

   ![Schakel beveiligde invoer of beveiligde uitvoer in](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   In de actie of de trigger wordt nu een vergrendelings pictogram weer gegeven in de titel balk.

   ![De titel balk van de actie of de trigger toont het vergrendelings pictogram](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens die beveiligde uitvoer van vorige acties vertegenwoordigen, worden ook vergrendelings pictogrammen weer gegeven. Wanneer u bijvoorbeeld een dergelijke uitvoer van de lijst met dynamische inhoud selecteert die u wilt gebruiken in een actie, toont dat token een vergrendelings pictogram.

   ![Token selecteren voor beveiligde uitvoer](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering weer geven.

   1. Selecteer in het **overzichts** venster van de logische app de uitvoering die u wilt weer geven.

   1. Vouw in het deel venster voor het uitvoeren van de **logische app** de acties uit die u wilt controleren.

      Als u hebt gekozen voor het verbergen van beide invoer en uitvoer, worden deze waarden nu verborgen weer gegeven.

      ![Verborgen invoer en uitvoer in uitvoerings geschiedenis](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Invoer en uitvoer in de code weergave beveiligen

In de onderliggende trigger of actie definitie kunt u de matrix toevoegen of bijwerken `runtimeConfiguration.secureData.properties` met een van beide of beide van de volgende waarden:

* `"inputs"`: Beveiligt invoer in uitvoerings geschiedenis.
* `"outputs"`: Hiermee worden uitvoer in uitvoerings geschiedenis beveiligd.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Toegang tot parameter invoer

Als u in verschillende omgevingen implementeert, kunt u de waarden in uw werk stroom definitie parameterizingen die variëren op basis van deze omgevingen. Op die manier kunt u hardcoded gegevens voor komen door een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) te gebruiken om uw logische app te implementeren, gevoelige gegevens te beschermen door beveiligde para meters te definiëren en deze gegevens als afzonderlijke invoer door te geven via de [para meters van de sjabloon](../azure-resource-manager/templates/template-parameters.md) met behulp van een [parameter bestand](../azure-resource-manager/templates/parameter-files.md).

Als u bijvoorbeeld HTTP-acties met [Azure Active Directory open-verificatie](#azure-active-directory-oauth-authentication) (Azure AD OAuth) verifieert, kunt u de para meters die de client-id en het client geheim accepteren die worden gebruikt voor verificatie, definiëren en verbergen. Als u deze para meters in uw logische app wilt definiëren, gebruikt u de `parameters` sectie in de werk stroom definitie van de logische app en de Resource Manager-sjabloon voor implementatie. Als u de parameter waarden die u niet wilt weer geven tijdens het bewerken van de logische app of de weer gave van de uitvoerings geschiedenis wilt helpen beveiligen, definieert u de para meters met behulp van de- `securestring` of- `secureobject` type en gebruikt u code ring. Para meters van dit type worden niet geretourneerd met de resource definitie en zijn niet toegankelijk wanneer de resource wordt weer gegeven na de implementatie. Als u tijdens runtime toegang wilt krijgen tot deze parameter waarden, gebruikt u de `@parameters('<parameter-name>')` expressie in de definitie van uw werk stroom. Deze expressie wordt alleen geëvalueerd tijdens runtime en wordt beschreven door de [taal van de werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Als u een para meter gebruikt in een aanvraag header of hoofd tekst, is die para meter mogelijk zichtbaar wanneer u de uitvoerings geschiedenis van de logische app en uitgaande HTTP-aanvraag weergeeft. Zorg ervoor dat u ook uw beleids regels voor inhouds toegang dienovereenkomstig instelt.
> U kunt ook een afkorting [gebruiken om](#obfuscate) invoer en uitvoer in de uitvoerings geschiedenis te verbergen. Autorisatie headers worden nooit weer gegeven via invoer of uitvoer. Dus als er een geheim wordt gebruikt, kan dat geheim niet worden opgehaald.

Zie voor meer informatie deze secties in dit onderwerp:

* [Veilige para meters in werk stroom definities](#secure-parameters-workflow)
* [Gegevens in de uitvoerings geschiedenis beveiligen met behulp van een afvallen](#obfuscate)

Als u de [implementatie van Logic apps automatiseert met behulp van Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u de [para meters](../azure-resource-manager/templates/template-parameters.md)voor beveiligde sjablonen definiëren die tijdens de implementatie worden geëvalueerd met behulp van de-en- `securestring` `secureobject` typen. Voor het definiëren van sjabloon parameters gebruikt u de sectie van het hoogste niveau van uw sjabloon `parameters` . deze is gescheiden en verschilt van de sectie van de werk stroom definitie `parameters` . Als u de waarden voor sjabloon parameters wilt opgeven, gebruikt u een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md).

Als u bijvoorbeeld geheimen gebruikt, kunt u de para meters voor beveiligde sjablonen definiëren en gebruiken om deze geheimen op te halen uit [Azure Key Vault](../key-vault/general/overview.md) tijdens de implementatie. U kunt vervolgens naar de sleutel kluis en het geheim in het parameter bestand verwijzen. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Gevoelige waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Veilige para meters in azure Resource Manager sjablonen](#secure-parameters-deployment-template) verderop in dit onderwerp

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Veilige para meters in werk stroom definities

Als u gevoelige informatie in de werk stroom definitie van de logische app wilt beveiligen, gebruikt u beveiligde para meters zodat deze informatie niet zichtbaar is nadat u uw logische app hebt opgeslagen. Stel dat u voor een HTTP-actie basis verificatie nodig hebt, waarbij een gebruikers naam en wacht woord worden gebruikt. In de definitie van de werk stroom `parameters` definieert de sectie de `basicAuthPasswordParam` en- `basicAuthUsernameParam` para meters met behulp van het `securestring` type. De actie definitie verwijst vervolgens naar deze para meters in de `authentication` sectie.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Veilige para meters in Azure Resource Manager sjablonen

Een [Resource Manager-sjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) voor een logische app heeft meerdere `parameters` secties. Als u wacht woorden, sleutels, geheimen en andere gevoelige informatie wilt beveiligen, definieert u de beveiligde para meters op sjabloon niveau en op het niveau van de werk stroom definitie met behulp van het `securestring` of- `secureobject` type. U kunt deze waarden vervolgens opslaan in [Azure Key Vault](../key-vault/general/overview.md) en het [parameter bestand](../azure-resource-manager/templates/parameter-files.md) gebruiken om te verwijzen naar de sleutel kluis en het geheim. Uw sjabloon haalt vervolgens die informatie op tijdens de implementatie. Zie voor meer informatie [gevoelige waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Hier vindt u meer informatie over deze `parameters` secties:

* Op het hoogste niveau van de sjabloon `parameters` definieert een sectie de para meters voor de waarden die de sjabloon gebruikt bij de *implementatie*. Deze waarden kunnen bijvoorbeeld verbindings reeksen bevatten voor een specifieke implementatie omgeving. U kunt deze waarden vervolgens opslaan in een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md), waardoor het wijzigen van deze waarden eenvoudiger wordt.

* In de resource definitie van uw logische app, maar buiten uw werk stroom definitie, `parameters` geeft een sectie de waarden voor de para meters van uw werk stroom definitie op. In deze sectie kunt u deze waarden toewijzen met behulp van sjabloon expressies die verwijzen naar de para meters van uw sjabloon. Deze expressies worden geëvalueerd tijdens de implementatie.

* In de definitie van de werk stroom `parameters` definieert een sectie de para meters die door de logische app worden gebruikt tijdens runtime. U kunt vervolgens naar deze para meters in de werk stroom van de logische app verwijzen met behulp van definitie expressies voor werk stromen, die tijdens runtime worden geëvalueerd.

Deze voorbeeld sjabloon met meerdere beveiligde parameter definities die gebruikmaken van het `securestring` type:

| Parameternaam | Beschrijving |
|----------------|-------------|
| `TemplatePasswordParam` | Een sjabloon parameter die vervolgens een wacht woord accepteert dat vervolgens wordt door gegeven aan de para meter van de werk stroom definitie `basicAuthPasswordParam` |
| `TemplateUsernameParam` | Een sjabloon parameter die vervolgens een gebruikers naam accepteert die vervolgens wordt door gegeven aan de para meter van de werk stroom definitie `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | Een para meter voor de werk stroom definitie die het wacht woord voor basis verificatie in een HTTP-actie accepteert |
| `basicAuthUserNameParam` | Een para meter voor de werk stroom definitie die de gebruikers naam voor basis verificatie in een HTTP-actie accepteert |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Toegang tot uitgaande oproepen naar andere services en systemen

Op basis van de mogelijkheid van het doel eindpunt, worden uitgaande oproepen verzonden door de [http-trigger of HTTP-actie](../connectors/connectors-native-http.md), wordt versleuteling ondersteund en beveiligd met [Transport Layer Security (TLS) 1,0, 1,1 of 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), voorheen bekend als Secure Sockets Layer (SSL). Logic Apps onderhandelt met het doel eindpunt met behulp van de hoogst mogelijke versie die wordt ondersteund. Als het doel eindpunt bijvoorbeeld 1,2 ondersteunt, gebruikt de HTTP-trigger of actie eerst 1,2. Anders gebruikt de connector de eerstvolgende hoogste ondersteunde versie.

Hier vindt u informatie over zelf-ondertekende TLS/SSL-certificaten:

* Voor Logic apps in de wereld wijde Azure-omgeving met meerdere tenants, de HTTP-connector staat niet-ondertekende TLS/SSL-certificaten niet toe. Als uw logische app een HTTP-aanroep naar een server maakt en een zelfondertekend TLS/SSL-certificaat presenteert, mislukt de HTTP-aanroep met een `TrustFailure` fout.

* Voor Logic apps in een [Integration service-omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de http-connector zelfondertekende certificaten voor TLS/SSL-Handshake toegestaan. U moet echter eerst [zelfondertekende ondersteuning voor certificaten inschakelen](../logic-apps/create-integration-service-environment-rest-api.md#request-body) voor een bestaande ISE of nieuwe ISE met behulp van de Logic apps rest API en het open bare certificaat op de `TrustedRoot` locatie installeren.

Hier vindt u meer manieren waarop u eind punten kunt beveiligen die gesp rekken afhandelen die vanuit uw logische app worden verzonden:

* [Verificatie toevoegen aan uitgaande aanvragen](#add-authentication-outbound).

  Wanneer u de HTTP-trigger of actie gebruikt voor het verzenden van uitgaande oproepen, kunt u verificatie toevoegen aan de aanvraag die wordt verzonden door uw logische app. U kunt bijvoorbeeld de volgende verificatie typen selecteren:

  * [Basisverificatie](#basic-authentication)

  * [Verificatie van client certificaten](#client-certificate-authentication)

  * [Active Directory OAuth-verificatie](#azure-active-directory-oauth-authentication)

  * [Verificatie van beheerde identiteit](#managed-identity-authentication)

* Beperk de toegang tot IP-adressen van logische apps.

  Alle aanroepen naar eind punten van Logic apps zijn afkomstig van specifieke IP-adressen die zijn gebaseerd op de regio's van uw Logic apps. U kunt filters toevoegen die alleen aanvragen van deze IP-adressen accepteren. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md#firewall-ip-configuration)om deze IP-adressen op te halen.

* Verbeter de beveiliging van verbindingen met on-premises systemen.

  Azure Logic Apps biedt integratie met deze services om veiligere en betrouw bare on-premises communicatie te bieden.

  * On-premises gegevensgateway

    Veel beheerde connectors in Azure Logic Apps ondersteunen beveiligde verbindingen met on-premises systemen, zoals bestands systeem, SQL, share point en DB2. De Gateway verzendt gegevens via on-premises bronnen op versleutelde kanalen via de Azure Service Bus. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. Meer informatie [over de werking van de on-premises gegevens gateway](logic-apps-gateway-install.md#gateway-cloud-service).

  * Verbinding maken via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) biedt opties voor on-premises verbindingen, zoals site-naar-site virtueel particulier netwerk en [ExpressRoute](../expressroute/expressroute-introduction.md) -integratie voor beveiligde proxy en communicatie met on-premises systemen. Als u een API hebt die toegang biedt tot uw on-premises systeem en u deze API beschikbaar maakt door een [API Management service-exemplaar](../api-management/get-started-create-service-instance.md)te maken, kunt u die API aanroepen in de werk stroom van de logische app door de ingebouwde API Management trigger of actie te selecteren in de ontwerp functie voor logische apps.

    > [!NOTE]
    > De connector toont alleen de API Management Services waarvoor u machtigingen hebt om te bekijken en er verbinding mee te maken, maar geen API Management Services op basis van verbruik worden weer gegeven.

    1. Typ in het vak Zoeken in de ontwerp functie voor logische apps `api management` . Kies de stap op basis van het feit of u een trigger of een actie wilt toevoegen:<p>

       * Als u een trigger wilt toevoegen, die altijd de eerste stap in de werk stroom is, selecteert u **een Azure API Management trigger kiezen**.

       * Als u een actie wilt toevoegen, selecteert u **een Azure-API management actie kiezen**.

       In dit voor beeld wordt een trigger toegevoegd:

       ![Azure API Management-trigger toevoegen](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Selecteer uw eerder gemaakte API Management service-exemplaar.

       ![API Management service-exemplaar selecteren](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Selecteer de API-aanroep die u wilt gebruiken.

       ![Bestaande API selecteren](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Verificatie toevoegen aan uitgaande oproepen

HTTP-en HTTPS-eind punten ondersteunen verschillende soorten verificatie. Bij sommige triggers en acties die u gebruikt voor het verzenden van uitgaande oproepen of aanvragen naar deze eind punten, kunt u een verificatie type opgeven. In de ontwerp functie voor logische apps hebben triggers en acties die ondersteuning bieden voor het kiezen van een verificatie type een **verificatie** -eigenschap. Deze eigenschap wordt echter mogelijk niet standaard altijd weer gegeven. In deze gevallen opent u de lijst **nieuwe para meter toevoegen** op de trigger of actie en selecteert u **verificatie**.

> [!IMPORTANT]
> Voor het beveiligen van gevoelige informatie die uw logische app afhandelt, gebruikt u beveiligde para meters en codeer gegevens als dat nodig is.
> Zie [toegang tot para meters invoeren](#secure-action-parameters)voor meer informatie over het gebruik en het beveiligen van para meters.

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>Verificatie typen voor triggers en acties die ondersteuning bieden voor verificatie

Deze tabel bevat de verificatie typen die beschikbaar zijn voor de triggers en acties waarbij u een verificatie type kunt selecteren:

| Verificatietype | Ondersteunde triggers en acties |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Azure API Management, Azure-app Services, HTTP, HTTP + Swagger, HTTP-webhook |
| [Client certificaat](#client-certificate-authentication) | Azure API Management, Azure-app Services, HTTP, HTTP + Swagger, HTTP-webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Onbewerkt](#raw-authentication) | Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Beheerde identiteit](#managed-identity-authentication) | **Ingebouwde triggers en acties** <p><p>Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP-webhook <p><p>**Beheerde connectors** <p><p>Azure AD Identity Protection, Azure Automation, Azure container instance, Azure Data Explorer, Azure Data Factory, Azure Data Lake, Azure Event Grid, Azure IoT Central v3, Azure Key Vault, Azure Resource Manager, Azure Sentinel, HTTP met Azure AD <p><p>**Opmerking**: ondersteuning voor beheerde connectors is momenteel beschikbaar als preview-versie. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Basisverificatie

Als de optie [basis](../active-directory-b2c/secure-rest-api.md) beschikbaar is, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Basic | Het te gebruiken verificatietype |
| **Gebruikersnaam** | `username` | Ja | <*gebruikers naam*>| De gebruikers naam voor het verifiëren van de toegang tot het eind punt van de doel service |
| **Wachtwoord** | `password` | Ja | <*wacht woord*> | Het wacht woord voor het verifiëren van de toegang tot het eind punt van de doel service |
||||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het verwerken en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie wordt de verificatie opgegeven `type` als `Basic` en wordt de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) gebruikt om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>Verificatie van client certificaten

Als de optie [client certificaat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) beschikbaar is, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Client certificaat** <br>of <br>`ClientCertificate` | Het verificatie type dat moet worden gebruikt. U kunt certificaten beheren met [Azure API Management](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Opmerking**: aangepaste connectors bieden geen ondersteuning voor verificatie op basis van certificaten voor zowel binnenkomende als uitgaande oproepen. |
| **Pfx** | `pfx` | Ja | <*encoded-pfx-file-content*> | De met base64 gecodeerde inhoud van een PFX-bestand (Personal Information Exchange) <p><p>Als u het PFX-bestand wilt converteren naar een base64-gecodeerde indeling, kunt u Power shell gebruiken door de volgende stappen uit te voeren: <p>1. Sla de certificaat inhoud op in een variabele: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converteer de certificaat inhoud met behulp van de `ToBase64String()` functie en sla die inhoud op in een tekst bestand: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Problemen oplossen**: als u de `cert mmc/PowerShell` opdracht gebruikt, kan deze fout optreden: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>U kunt deze fout oplossen door het PFX-bestand te converteren naar een PEM-bestand en weer terug te zetten met behulp van de `openssl` opdracht: <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Wanneer u daarna de base64-gecodeerde teken reeks voor het zojuist geconverteerde PFX-bestand van het certificaat krijgt, werkt de teken reeks nu in Azure Logic Apps. |
| **Wachtwoord** | `password`| Nee | <*wacht woord voor pfx-bestand*> | Het wacht woord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het verwerken en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie wordt de verificatie opgegeven `type` als `ClientCertificate` en wordt de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) gebruikt om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Zie de volgende onderwerpen voor meer informatie over het beveiligen van services met behulp van verificatie via client certificaten:

* [Verbeter de beveiliging van Api's met behulp van verificatie op basis van client certificaten in azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Verbeter de beveiliging van back-end-services door gebruik te maken van verificatie van client certificaten in azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Verbeter de beveiliging van uw REST-service door gebruik te maken van client certificaten](../active-directory-b2c/secure-rest-api.md)
* [Certificaat referenties voor toepassings verificatie](../active-directory/develop/active-directory-certificate-credentials.md)
* [Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Verificatie Azure Active Directory openen

Op aanvraag triggers kunt u [Azure Active Directory open-verificatie (Azure AD OAuth)](../active-directory/develop/index.yml)gebruiken voor de verificatie van binnenkomende oproepen nadat u een [Azure AD-autorisatie beleid hebt ingesteld](#enable-oauth) voor uw logische app. Voor alle andere triggers en acties die het **Active Directory OAuth** -verificatie type voor u selecteren, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Active Directory OAuth** <br>of <br>`ActiveDirectoryOAuth` | Het verificatie type dat moet worden gebruikt. Het [OAuth 2,0-protocol](../active-directory/develop/v2-overview.md)wordt momenteel gevolgd door Logic apps. |
| **Instantie** | `authority` | Nee | <*URL-voor-Authority-token-Uitgever*> | De URL voor de instantie die het toegangs token levert. Deze waarde is standaard ingesteld op `https://login.windows.net` . |
| **Bouw** | `tenant` | Ja | <*Tenant-ID*> | De Tenant-ID voor de Azure AD-Tenant |
| **Doelgroep** | `audience` | Ja | <*resource-naar-autoriseren*> | De resource die u wilt gebruiken voor autorisatie, bijvoorbeeld `https://management.core.windows.net/` |
| **Client ID** | `clientId` | Ja | <*client-ID*> | De client-ID voor de app die autorisatie aanvraagt |
| **Referentie type** | `credentialType` | Ja | Certificaat <br>of <br>Geheim | Het referentie type dat door de client wordt gebruikt voor het aanvragen van autorisatie. Deze eigenschap en waarde worden niet weer gegeven in de onderliggende definitie van de logische app, maar bepaalt de eigenschappen die worden weer gegeven voor het geselecteerde referentie type. |
| **Gescheiden** | `secret` | Ja, maar alleen voor het referentie type ' geheim ' | <*client-geheim*> | Het client geheim voor het aanvragen van autorisatie |
| **Pfx** | `pfx` | Ja, maar alleen voor het referentie type ' certificaat ' | <*encoded-pfx-file-content*> | De met base64 gecodeerde inhoud van een PFX-bestand (Personal Information Exchange) |
| **Wachtwoord** | `password` | Ja, maar alleen voor het referentie type ' certificaat ' | <*wacht woord voor pfx-bestand*> | Het wacht woord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het verwerken en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie wordt de verificatie ingesteld `type` als `ActiveDirectoryOAuth` , het referentie type als en `Secret` wordt de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) gebruikt om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Onbewerkte authenticatie

Als de **onbewerkte** optie beschikbaar is, kunt u dit verificatie type gebruiken wanneer u [verificatie schema's](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) moet gebruiken die niet voldoen aan het [OAuth 2,0-protocol](https://oauth.net/2/). Met dit type kunt u hand matig de waarde voor de autorisatie-header maken die u met de uitgaande aanvraag verzendt en die header waarde opgeven in de trigger of actie.

Hier ziet u een voor beeld van een header voor een HTTPS-aanvraag die volgt op het [OAuth 1,0-protocol](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Geef in de trigger of actie die onbewerkte authenticatie ondersteunt de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Onbewerkt | Het te gebruiken verificatietype |
| **Waarde** | `value` | Ja | <*autorisatie-header-waarde*> | De waarde van de autorisatie-header die moet worden gebruikt voor verificatie |
||||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het verwerken en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld wordt de HTTP-actie definitie opgegeven `type` als `Raw` , en wordt de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) gebruikt om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Verificatie van beheerde identiteit

Wanneer de optie [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) beschikbaar is in de [trigger of actie die beheerde identiteits verificatie ondersteunt](#add-authentication-outbound), kan uw logische app gebruikmaken van de door het systeem toegewezen identiteit of een *enkele* hand matig gemaakte door de gebruiker toegewezen identiteit voor het verifiëren van toegang tot Azure-bronnen die worden beveiligd door Azure Active Directory (Azure AD), in plaats van referenties, geheimen of Azure AD-tokens. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen beheert of rechtstreeks Azure AD-tokens gebruikt. Meer informatie over [Azure-Services die ondersteuning bieden voor beheerde identiteiten voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Voordat uw logische app een beheerde identiteit kan gebruiken, volgt u de stappen in [toegang tot Azure-resources verifiëren door beheerde identiteiten te gebruiken in azure Logic apps](../logic-apps/create-managed-service-identity.md). Met deze stappen wordt de beheerde identiteit voor uw logische app ingeschakeld en wordt de toegang tot de Azure-doel bron ingesteld.

1. Voordat een Azure-functie een beheerde identiteit kan gebruiken, moet u eerst [verificatie voor Azure functions inschakelen](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. Geef de volgende informatie op in de trigger of actie die ondersteuning biedt voor het gebruik van een beheerde identiteit:

   **Ingebouwde triggers en acties**

   | Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
   |---------------------|-----------------|----------|-------|-------------|
   | **Verificatie** | `type` | Ja | **Beheerde identiteit** <br>of <br>`ManagedServiceIdentity` | Het te gebruiken verificatietype |
   | **Beheerde identiteit** | `identity` | Ja | * **Door het systeem toegewezen beheerde identiteit** <br>of <br>`SystemAssigned` <p><p>* <door de *gebruiker toegewezen identiteits naam*> | De beheerde identiteit die moet worden gebruikt |
   | **Doelgroep** | `audience` | Ja | <*doel-Resource-ID*> | De resource-ID voor de doel resource waartoe u toegang wilt krijgen. <p>`https://storage.azure.com/`Maakt bijvoorbeeld de [toegangs tokens](../active-directory/develop/access-tokens.md) voor verificatie geldig voor alle opslag accounts. U kunt echter ook een URL voor de basis service opgeven, zoals `https://fabrikamstorageaccount.blob.core.windows.net` voor een specifiek opslag account. <p>**Opmerking**: de eigenschap **doel groep** kan in sommige triggers of acties worden verborgen. Als u deze eigenschap zichtbaar wilt maken, opent u de lijst **nieuwe para meter toevoegen** in de trigger of actie en selecteert u **doel groep**. <p><p>**Belang rijk**: Zorg ervoor dat deze doel resource-id *precies overeenkomt* met de waarde die door Azure AD wordt verwacht, inclusief alle vereiste afsluitende slashes. De `https://storage.azure.com/` resource-id voor alle Azure Blob Storage-accounts vereist dus een afsluitende slash. De resource-ID voor een specifiek opslag account vereist echter geen afsluitende slash. Zie [Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)om deze resource-id's te vinden. |
   |||||

   Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het verwerken en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. Met deze HTTP-actie definitie geeft u bijvoorbeeld de `type` verificatie `ManagedServiceIdentity` op als en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Triggers en acties van beheerde connector**

   | Eigenschap (Designer) | Vereist | Waarde | Beschrijving |
   |---------------------|----------|-------|-------------|
   | **Verbindings naam** | Ja | <*verbindings naam*> ||
   | **Beheerde identiteit** | Ja | **Door het systeem toegewezen beheerde identiteit** <br>of <br> <*door de gebruiker toegewezen beheerde identiteits naam*> | Het te gebruiken verificatietype |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Maken van verbindingen blok keren

Als uw organisatie geen verbinding met specifieke bronnen kan maken met behulp van hun connectors in Azure Logic Apps, kunt u [de mogelijkheid blok keren om deze verbindingen](../logic-apps/block-connections-connectors.md) voor specifieke connectors in de werk stromen van logische apps te gebruiken met behulp van [Azure Policy](../governance/policy/overview.md). Zie voor meer informatie [blok keren verbindingen die zijn gemaakt door specifieke connectors in azure Logic apps](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Isolatie richtlijnen voor Logic apps

U kunt Azure Logic Apps gebruiken in [Azure Government](../azure-government/documentation-government-welcome.md) alle impact niveaus in de regio's die worden beschreven door de [Azure Government impact niveau 5 isolatie richtlijnen](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) en het [Amerikaanse ministerie van defensie srg (Security Requirements Guide) voor Cloud Computing](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Om aan deze vereisten te voldoen, ondersteunt Logic Apps de mogelijkheid om werk stromen te maken en uit te voeren in een omgeving met speciale resources, zodat u de prestaties van andere Azure-tenants in uw Logic apps kunt verminderen en het delen van computer bronnen met andere tenants vermijdt.

* Als u uw eigen code wilt uitvoeren of XML-trans formatie wilt uitvoeren, [maakt u een Azure-functie en roept](../logic-apps/logic-apps-azure-functions.md)u deze aan in plaats van de functie voor [inline code](../logic-apps/logic-apps-add-run-inline-code.md) te gebruiken of [om assembly's respectievelijk te gebruiken als kaarten](../logic-apps/logic-apps-enterprise-integration-maps.md). Stel ook de hostomgeving in voor uw functie-app om te voldoen aan uw isolatie vereisten.

  Om bijvoorbeeld te voldoen aan de vereisten van impact niveau 5, maakt u uw functie-app met het [app service plan](../azure-functions/dedicated-plan.md) met behulp van de [ **geïsoleerde** prijs categorie](../app-service/overview-hosting-plans.md) samen met een [app service Environment (ASE)](../app-service/environment/intro.md) die ook gebruikmaakt van de **geïsoleerde** prijs categorie. In deze omgeving werken apps op specifieke virtuele machines van Azure en speciale virtuele Azure-netwerken, die netwerk isolatie bieden naast reken isolatie voor uw apps en maximale schaal mogelijkheden. Zie [Azure Government impact niveau 5-Azure functions](../azure-government/documentation-government-impact-level-5.md#azure-functions)voor meer informatie.

  Raadpleeg de volgende onderwerpen voor meer informatie:<p>

  * [Azure App Service plannen](../app-service/overview-hosting-plans.md)
  * [Netwerkopties van Azure Functions](../azure-functions/functions-networking-options.md)
  * [Voor Azure toegewezen hosts voor virtuele machines](../virtual-machines/dedicated-hosts.md)
  * [Isolatie van virtuele machines in azure](../virtual-machines/isolation.md)
  * [Speciale Azure-Services implementeren in virtuele netwerken](../virtual-network/virtual-network-for-azure-services.md)

* Als u logische Apps wilt maken die worden uitgevoerd op toegewezen resources en toegang hebben tot bronnen die worden beveiligd met een virtueel Azure-netwerk, kunt u een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)maken.

  * Sommige virtuele netwerken van Azure gebruiken privé-eind punten ([persoonlijke Azure-koppeling](../private-link/private-link-overview.md)) om toegang te bieden tot Azure PaaS-Services, zoals Azure Storage, Azure Cosmos DB of Azure SQL database, partner services of klanten services die worden gehost op Azure. Als uw Logic apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eind punten, moet u deze Logic apps maken, implementeren en uitvoeren in een ISE.

  * Als u meer controle wilt over de versleutelings sleutels die worden gebruikt door Azure Storage, kunt u uw eigen sleutel instellen, gebruiken en beheren met behulp van [Azure Key Vault](../key-vault/general/overview.md). Deze mogelijkheid is ook bekend als ' Bring Your Own Key ' (BYOK) en uw sleutel wordt een ' door de klant beheerde sleutel ' genoemd. Zie voor meer informatie door de [klant beheerde sleutels instellen voor het versleutelen van gegevens in rust voor integratie service omgevingen (ISEs) in azure Logic apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Isolatie in de open bare Azure-Cloud](../security/fundamentals/isolation-choices.md)
* [Beveiliging voor uiterst gevoelige IaaS-apps in azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Volgende stappen

* [Azure-beveiligings basislijn voor Azure Logic Apps](../logic-apps/security-baseline.md)
* [Implementatie voor Azure Logic Apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Logische apps bewaken](../logic-apps/monitor-logic-apps-log-analytics.md)
