---
title: De ontwikkelaarsportal zelf hosten
titleSuffix: Azure API Management
description: Meer informatie over het zelf hosten van API Management ontwikkelaarsportal.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741715"
---
# <a name="self-host-the-api-management-developer-portal"></a>Zelf hosten van de API Management-ontwikkelaarsportal

In deze zelfstudie wordt beschreven hoe u de API Management [portal voor ontwikkelaars zelf host.](api-management-howto-developer-portal.md) Zelfhosting biedt u de flexibiliteit om de ontwikkelaarsportal uit te breiden met aangepaste logica en widgets die pagina's dynamisch aanpassen tijdens runtime. U kunt meerdere portals voor uw API Management hosten, met verschillende functies. Wanneer u een portal zelf host, wordt u de onderhouder en bent u verantwoordelijk voor de upgrades. 

De volgende stappen laten zien hoe u uw lokale ontwikkelomgeving in kunt stellen, wijzigingen kunt aanbrengen in de ontwikkelaarsportal en deze kunt publiceren en implementeren in een Azure-opslagaccount.

Zie Move [from managed to self-hosted](#move-from-managed-to-self-hosted-developer-portal)(Verplaatsen van beheerd naar zelf-hostend) verderop in dit artikel als u al mediabestanden hebt geüpload of gewijzigd in de beheerde portal.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

Als u een lokale ontwikkelomgeving wilt instellen, hebt u het volgende nodig:

- Een API Management service-exemplaar. Als u er nog geen hebt, zie [Snelstart : Een Azure API Management maken.](get-started-create-service-instance.md)
- Een Azure-opslagaccount [met de functie statische websites](../storage/blobs/storage-blob-static-website.md) ingeschakeld. Zie [Een opslagaccount maken.](../storage/common/storage-account-create.md)
- Git op uw computer. Installeer deze door deze [Git-zelfstudie te volgen.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Node.js (LTS-versie `v10.15.0` of hoger) en npm op uw computer. Zie [Downloaden en installeren Node.js en npm.](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Azure CLI. Volg [de azure CLI-installatiestappen.](/cli/azure/install-azure-cli-windows)

## <a name="step-1-set-up-local-environment"></a>Stap 1: lokale omgeving instellen

Als u uw lokale omgeving wilt instellen, moet u de opslagplaats klonen, overschakelen naar de nieuwste versie van de ontwikkelaarsportal en npm-pakketten installeren.

1. Kloon [de opslagplaats api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) vanuit GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Ga naar uw lokale kopie van de repo:

    ```console
    cd api-management-developer-portal
    ```

1. Bekijk de nieuwste versie van de portal.

    Voordat u de volgende code gaat uitvoeren, controleert u de huidige releasetag in de [sectie Releases](https://github.com/Azure/api-management-developer-portal/releases) van de opslagplaats en vervangt u de waarde door de `<current-release-tag>` nieuwste releasetag.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Installeer beschikbare npm-pakketten:

    ```console
    npm install
    ```

> [!TIP]
> Gebruik altijd de [nieuwste versie van de portal](https://github.com/Azure/api-management-developer-portal/releases) en houd uw gevorkte portal up-to-date. De softwaretechnici gebruiken `master` de vertakking van deze opslagplaats voor dagelijkse ontwikkelingsdoeleinden. Het heeft instabiele versies van de software.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Stap 2: JSON-bestanden, statische website en CORS-instellingen configureren

De ontwikkelaarsportal vereist API Management van REST API om de inhoud te beheren.

### <a name="configdesignjson-file"></a>config.design.jsin het bestand

Ga naar de `src` map en open het `config.design.json` bestand.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Configureer het bestand:

1. Vervang in `managementApiUrl` de waarde door de naam van uw API Management `<service-name>` exemplaar. Als u een aangepast domein [hebt geconfigureerd,](configure-custom-domain.md)gebruikt u dit in plaats daarvan (bijvoorbeeld `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Maak handmatig een SAS-token](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) om de directe toegang REST API uw API Management in te stellen.

1. Kopieer het gegenereerde token en plak het als `managementApiAccessToken` de waarde.

1. Vervang in `backendUrl` de waarde door de naam van uw API Management `<service-name>` exemplaar. Als u een aangepast domein [hebt geconfigureerd,](configure-custom-domain.md)gebruikt u dit in plaats daarvan (bijvoorbeeld `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Zie ENABLE CAPTCHA (CAPTCHA inschakelen) als u CAPTCHA wilt inschakelen in uw [ontwikkelaarsportal.](#enable-captcha)

### <a name="configpublishjson-file"></a>config.publish.jsin het bestand

Ga naar de `src` map en open het `config.publish.json` bestand.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Configureer het bestand:

1. Kopieer en plak de `managementApiUrl` waarden en uit het vorige `managementApiAccessToken` configuratiebestand.

1. Zie ENABLE CAPTCHA (CAPTCHA inschakelen) als u CAPTCHA wilt inschakelen in uw [ontwikkelaarsportal.](#enable-captcha)

### <a name="configruntimejson-file"></a>config.runtime.jsin het bestand

Ga naar de `src` map en open het `config.runtime.json` bestand.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Configureer het bestand:

1. Kopieer en plak de `managementApiUrl` waarde uit het vorige configuratiebestand.

1. Vervang in `backendUrl` de waarde door de naam van uw API Management `<service-name>` exemplaar. Als u een aangepast domein [hebt geconfigureerd,](configure-custom-domain.md)gebruikt u dit in plaats daarvan (bijvoorbeeld. `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>De statische website configureren

Configureer de **functie Statische website** in uw opslagaccount door routes op te geven naar de index en foutpagina's:

1. Ga naar uw opslagaccount in Azure Portal en selecteer **Statische website** in het menu aan de linkerkant.

1. Selecteer ingeschakeld **op de** pagina **Statische website.**

1. Voer in **het veld Naam van het indexdocument** index.htm *l* in.

1. Voer in **het veld Foutdocumentpad** *404/index.html in.*

1. Selecteer **Opslaan**.

### <a name="configure-the-cors-settings"></a>De CORS-instellingen configureren

Configureer de CORS-instellingen (Cross-Origin Resource Sharing) :

1. Ga naar uw opslagaccount in Azure Portal selecteer **CORS** in het menu aan de linkerkant.

1. Configureer **op Blob service** tabblad De volgende regels:

    | Regel | Waarde |
    | ---- | ----- |
    | Toegestane oorsprongen | * |
    | Toegestane methoden | Selecteer alle HTTP-woorden. |
    | Toegestane headers | * |
    | Weergegeven headers | * |
    | Maximale leeftijd | 0 |

1. Selecteer **Opslaan**.

## <a name="step-3-run-the-portal"></a>Stap 3: de portal uitvoeren

U kunt nu een lokale portal-instantie bouwen en uitvoeren in de ontwikkelmodus. In de ontwikkelingsmodus worden alle optimalisaties uitgeschakeld en worden de bronkaarten ingeschakeld.

Voer de volgende opdracht uit:

```console
npm start
```

Na korte tijd wordt de standaardbrowser automatisch geopend met uw lokale exemplaar van de ontwikkelaarsportal. Het standaardadres is `http://localhost:8080` , maar de poort kan worden gewijzigd als deze al bezet `8080` is. Bij wijzigingen in de codebasis van het project wordt het browservenster opnieuw opgebouwd en vernieuwd.

## <a name="step-4-edit-through-the-visual-editor"></a>Stap 4: Bewerken via de visuele editor

Gebruik de visualeditor om deze taken uit te voeren:

- Uw portal aanpassen
- Inhoud schrijven
- De structuur van de website organiseren
- Het uiterlijk van de afbeelding is niet meer te zien

Zie [Zelfstudie: De ontwikkelaarsportal openen en aanpassen.](api-management-howto-developer-portal-customize.md) Het bevat de basisbeginselen van de gebruikersinterface voor beheer en bevat een lijst met aanbevolen wijzigingen in de standaardinhoud. Sla alle wijzigingen in de lokale omgeving op en druk **op Ctrl+C om** deze te sluiten.

## <a name="step-5-publish-locally"></a>Stap 5: Lokaal publiceren

De portalgegevens zijn afkomstig in de vorm van sterk getypeerd objecten. Met de volgende opdracht worden deze omgezet in statische bestanden en wordt de uitvoer in de map `./dist/website` opgeslagen:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Stap 6: Statische bestanden uploaden naar een blob

Gebruik Azure CLI om de lokaal gegenereerde statische bestanden te uploaden naar een blob en zorg ervoor dat uw bezoekers bij deze bestanden kunnen komen:

1. Open Windows-opdrachtprompt, PowerShell of een andere opdrachtshell.

1. Voer de volgende Azure CLI-opdracht uit.
   
    Vervang `<account-connection-string>` door de connection string van uw opslagaccount. U kunt deze ophalen uit de sectie **Toegangssleutels** van uw opslagaccount.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Stap 7: ga naar uw website

Uw website is nu live onder de hostnaam die is opgegeven in Azure Storage eigenschappen (**Primair** eindpunt in **statische websites).**

## <a name="step-8-change-api-management-notification-templates"></a>Stap 8: de API Management wijzigen

Vervang de URL van de ontwikkelaarsportal in de API Management om te wijzen naar uw zelf-hostende portal. Zie [Meldingen en e-mailsjablonen configureren in Azure API Management.](api-management-howto-configure-notifications.md)

Voer met name de volgende wijzigingen in de standaardsjablonen uit:

> [!NOTE]
> Bij de waarden in de volgende **secties** Bijgewerkt wordt ervan uitgenomen dat u de portal host op **https: \/ /portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Bevestiging van e-mailwijziging

Werk de URL van de ontwikkelaarsportal bij in de sjabloon **Bevestigingsmelding voor e-mailwijziging:**

**Oorspronkelijke inhoud**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Bijgewerkt**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Bevestiging van nieuw ontwikkelaarsaccount

Werk de URL van de ontwikkelaarsportal bij in **de sjabloon Bevestigingsmeldingen** voor nieuw ontwikkelaarsaccount:

**Oorspronkelijke inhoud**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Bijgewerkt**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Gebruiker uitnodigen

Werk de URL van de ontwikkelaarsportal bij in **de sjabloon Gebruikersmeldingen** uitnodigen:

**Oorspronkelijke inhoud**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Bijgewerkt**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Nieuw abonnement geactiveerd

Werk de URL van de ontwikkelaarsportal bij in **de meldingssjabloon Nieuw abonnement geactiveerd:**

**Oorspronkelijke inhoud**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Bijgewerkt**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Oorspronkelijke inhoud**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Bijgewerkt**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Oorspronkelijke inhoud**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Bijgewerkt**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Oorspronkelijke inhoud**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Bijgewerkt**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Bevestiging van wachtwoordwijziging

Werk de URL van de ontwikkelaarsportal bij in de sjabloon **Bevestigingsmeldingen voor wachtwoordwijziging:**

**Oorspronkelijke inhoud**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Bijgewerkt**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Alle sjablonen

Werk de URL van de ontwikkelaarsportal bij in een sjabloon met een koppeling in de voettekst:

**Oorspronkelijke inhoud**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Bijgewerkt**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Verplaatsen van beheerde naar zelf-hostende ontwikkelaarsportal

Na een periode kunnen uw bedrijfsvereisten veranderen. U kunt in een situatie komen waarin de beheerde versie van de API Management ontwikkelaarsportal niet meer aan uw behoeften voldoet. Een nieuwe vereiste kan u bijvoorbeeld dwingen een aangepaste widget te maken die kan worden geïntegreerd met een externe gegevensprovider. In tegenstelling tot de manged-versie biedt de zelf-hostende versie van de portal u volledige flexibiliteit en extensibility.

### <a name="transition-process"></a>Overgangsproces

U kunt overstappen van de beheerde versie naar een zelf-hostende versie binnen hetzelfde API Management service-exemplaar. Het proces behoudt de wijzigingen die u hebt uitgevoerd in de beheerde versie van de portal. Zorg ervoor dat u vooraf een back-up maakt van de inhoud van de portal. U vindt het back-upscript in de `scripts` map van API Management Developer Portal [GitHub-opslagplaats](https://github.com/Azure/api-management-developer-portal).

Het conversieproces is bijna identiek aan het instellen van een algemene zelf-hostende portal, zoals wordt weergegeven in de vorige stappen in dit artikel. Er is één uitzondering in de configuratiestap. Het opslagaccount in `config.design.json` het bestand moet hetzelfde zijn als het opslagaccount van de beheerde versie van de portal. Zie Zelfstudie: Een door het [Linux-VM-systeem](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) toegewezen identiteit gebruiken voor toegang tot Azure Storage via een SAS-referentie voor instructies over het ophalen van de SAS-URL.

> [!TIP]
> We raden u aan een afzonderlijk opslagaccount in het bestand te `config.publish.json` gebruiken. Deze aanpak biedt u meer controle en vereenvoudigt het beheer van de hostingservice van uw portal.

## <a name="enable-captcha"></a>CAPTCHA inschakelen

Bij het instellen van de zelf-hostende portal hebt u CAPTCHA mogelijk uitgeschakeld via de `useHipCaptcha` instelling. Communicatie met CAPTCHA vindt plaats via een eindpunt, waarmee Cross-Origin Resource Sharing (CORS) alleen kan plaatsvinden voor de hostnaam van de beheerde ontwikkelaarsportal. Als uw ontwikkelaarsportal zelf wordt gehost, gebruikt deze een andere hostnaam en staat CAPTCHA de communicatie niet toe.

### <a name="update-the-json-config-files"></a>De JSON-configuratiebestanden bijwerken

De CAPTCHA inschakelen in uw zelf-hostende portal:

1. Wijs een aangepast domein (bijvoorbeeld ) toe aan het eindpunt van de ontwikkelaarsportal `api.contoso.com` van uw  API Management service.

    Dit domein is van toepassing op de beheerde versie van uw portal en het CAPTCHA-eindpunt. Zie Configure [a custom domain name for your Azure API Management instance (Een aangepaste domeinnaam configureren voor uw Azure API Management-exemplaar).](configure-custom-domain.md)

1. Ga naar de `src` map in de lokale omgeving [voor](#step-1-set-up-local-environment) uw zelf-hostende portal.

1. Werk de `json` configuratiebestanden bij:

    | File | Nieuwe waarde | Notitie |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Vervang `<custom-domain>` door het aangepaste domein dat u in de eerste stap hebt ingesteld. |
    |  | `"useHipCaptcha": true` | Wijzig de waarde in `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Vervang `<custom-domain>` door het aangepaste domein dat u in de eerste stap hebt ingesteld. |
    |  | `"useHipCaptcha": true` | Wijzig de waarde in `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Vervang `<custom-domain>` door het aangepaste domein dat u in de eerste stap hebt ingesteld. |

1. [Publiceer](#step-5-publish-locally) de portal.

1. [Upload](#step-6-upload-static-files-to-a-blob) en host de zojuist gepubliceerde portal.

1. De zelf-hostende portal via een aangepast domein.

De eerste en tweede niveaus van het portaldomein moeten overeenkomen met het domein dat in de eerste stap is ingesteld. Bijvoorbeeld `portal.contoso.com`. De exacte stappen zijn afhankelijk van het hostingplatform van uw keuze. Als u een Azure-opslagaccount hebt gebruikt, raadpleegt u Een aangepast domein aan een Azure Blob Storage [eindpunt voor](../storage/blobs/storage-custom-domain-name.md) instructies.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over alternatieve benaderingen voor zelfhosting](developer-portal-alternative-processes-self-host.md)
