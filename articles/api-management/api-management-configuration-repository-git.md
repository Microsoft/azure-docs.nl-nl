---
title: Uw API Management-service configureren met git-Azure | Microsoft Docs
description: Meer informatie over het opslaan en configureren van uw API Management service configuratie met behulp van Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400349"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Configuratie van API Management-service opslaan en configureren met behulp van Git

Elk API Management service-exemplaar houdt een configuratie database bij die informatie bevat over de configuratie en meta gegevens voor het service-exemplaar. Wijzigingen kunnen worden aangebracht in het service-exemplaar door een instelling in het Azure Portal te wijzigen, met een Power shell-cmdlet of door een REST API-aanroep te maken. Naast deze methoden kunt u ook de configuratie van uw service-exemplaar beheren met git, waarbij scenario's voor Service beheer worden ingeschakeld, zoals:

* Configuratie versie: down load en sla verschillende versies van uw service configuratie op
* Wijzigingen in de bulk configuratie: Breng wijzigingen aan in meerdere delen van uw service configuratie in uw lokale opslag plaats en integreer de wijzigingen vervolgens met één bewerking op de server.
* Vertrouwde Git-hulpprogramma keten en-werk stroom: gebruik het git-hulp programma en de werk stromen die u al kent

In het volgende diagram ziet u een overzicht van de verschillende manieren om uw API Management service-exemplaar te configureren.

![Git configureren][api-management-git-configure]

Wanneer u wijzigingen aanbrengt aan uw service met behulp van de Azure Portal, Power shell-cmdlets of de REST API, beheert u de data base van de service configuratie met behulp `https://{name}.management.azure-api.net` van het eind punt, zoals wordt weer gegeven aan de rechter kant van het diagram. Aan de linkerkant van het diagram ziet u hoe u uw service configuratie kunt beheren met Git en git-opslag plaats voor uw service op `https://{name}.scm.azure-api.net` .

De volgende stappen bieden een overzicht van het beheren van uw API Management service-exemplaar met behulp van Git.

1. Toegang tot Git-configuratie in uw service
2. Sla uw service configuratie database op in uw Git-opslag plaats
3. De Git-opslag plaats naar uw lokale computer klonen
4. Haal de meest recente opslag plaats omlaag naar uw lokale machine en ga door met het door voeren en pushen van wijzigingen naar uw opslag plaats
5. De wijzigingen van uw opslag plaats naar uw service configuratie database implementeren

In dit artikel wordt beschreven hoe u Git inschakelt en gebruikt voor het beheren van uw service configuratie en een verwijzing geeft naar de bestanden en mappen in de Git-opslag plaats.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Toegang tot Git-configuratie in uw service

Als u uw Git-configuratie-instellingen wilt weer geven en configureren, klikt u op het menu **implementatie en infra structuur** en gaat u naar het tabblad **opslag plaats** .

![GIT inschakelen][api-management-enable-git]

> [!IMPORTANT]
> Geheimen die niet als benoemde waarden zijn gedefinieerd, worden opgeslagen in de-opslag plaats en blijven in de geschiedenis totdat u Git-toegang uitschakelt en weer inschakelt. Benoemde waarden bieden een veilige plaats voor het beheren van constante teken reeks waarden, inclusief geheimen, in alle API-configuraties en-beleids regels, zodat u ze niet rechtstreeks in uw beleids overzichten hoeft op te slaan. Zie [How to use named values in Azure API Management Policies](api-management-howto-properties.md)(Engelstalig) voor meer informatie.
>
>

Zie voor meer informatie over het in-of uitschakelen van Git-toegang met behulp van de REST API [toegang tot git in-of uitschakelen met behulp van de rest API](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>De service configuratie opslaan in de Git-opslag plaats

De eerste stap voordat u de opslag plaats kloont, is door de huidige status van de service configuratie op te slaan in de opslag plaats. Klik op **opslaan in opslag plaats**.

Breng de gewenste wijzigingen aan in het bevestigings scherm en klik op **Opslaan** om op te slaan.

Na enkele ogen blikken wordt de configuratie opgeslagen en wordt de configuratie status van de opslag plaats weer gegeven, met inbegrip van de datum en tijd van de laatste configuratie wijziging en de laatste synchronisatie tussen de service configuratie en de opslag plaats.

Zodra de configuratie is opgeslagen in de opslag plaats, kan deze worden gekloond.

Zie [configuratie momentopname vastleggen met behulp](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot)van de rest API voor meer informatie over het uitvoeren van deze bewerking met behulp van de rest API.

## <a name="to-clone-the-repository-to-your-local-machine"></a>De opslag plaats naar uw lokale computer klonen

Als u een opslag plaats wilt klonen, moet u de URL naar uw opslag plaats, een gebruikers naam en een wacht woord hebben. Als u gebruikers naam en andere referenties wilt ophalen, klikt u op **toegangs referenties** aan de bovenkant van de pagina.

Als u een wacht woord wilt genereren, controleert u eerst of het **verloop** is ingesteld op de gewenste verval datum en-tijd en klikt u vervolgens op **genereren**.

> [!IMPORTANT]
> Noteer dit wacht woord. Wanneer u deze pagina verlaat, wordt het wacht woord niet meer weer gegeven.
>

In de volgende voor beelden wordt het git bash-hulp programma van [git voor Windows](https://www.git-scm.com/downloads) gebruikt, maar u kunt elk Git-hulp programma gebruiken waarmee u bekend bent.

Open uw Git-hulp programma in de gewenste map en voer de volgende opdracht uit om de Git-opslag plaats te klonen op uw lokale computer met behulp van de opdracht van de Azure Portal.

```
git clone https://{name}.scm.azure-api.net/
```

Geef de gebruikers naam en het wacht woord op wanneer u hierom wordt gevraagd.

Als er fouten optreden, kunt u de `git clone` opdracht wijzigen zodat de gebruikers naam en het wacht woord worden opgenomen, zoals in het volgende voor beeld wordt weer gegeven.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Als er een fout optreedt, kunt u URL-code ring van het wachtwoord gedeelte van de opdracht proberen. Een snelle manier om dit te doen is door Visual Studio te openen en de volgende opdracht uit te voeren in het **venster Direct**. Als u het **venster Direct** wilt openen, opent u een oplossing of project in Visual Studio (of maakt u een nieuwe lege console toepassing) en kiest u **Windows**, **direct** in het menu **fout opsporing** .

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Gebruik het versleutelde wacht woord samen met uw gebruikers naam en locatie van de opslag plaats om de Git-opdracht te maken.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Zodra de opslag plaats is gekloond, kunt u deze in uw lokale bestands systeem weer geven en ermee werken. Zie [verwijzing naar bestands-en mapstructuur van lokale Git-opslag plaats](#file-and-folder-structure-reference-of-local-git-repository)voor meer informatie.

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Uw lokale opslag plaats bijwerken met de meest recente service-exemplaar configuratie

Als u wijzigingen aanbrengt in uw API Management service-exemplaar in de Azure Portal of de REST API gebruikt, moet u deze wijzigingen opslaan in de opslag plaats voordat u uw lokale opslag plaats kunt bijwerken met de meest recente wijzigingen. Als u dit wilt doen, klikt u op **opslaan in opslag plaats** op het tabblad **opslag plaats** in het Azure Portal en geeft u de volgende opdracht op in uw lokale opslag plaats.

```
git pull
```

`git pull`Zorg ervoor dat u zich in de map voor uw lokale opslag plaats bevindt voordat u deze uitvoert. Als u de opdracht zojuist hebt voltooid `git clone` , moet u de map in uw opslag plaats wijzigen door een opdracht als volgt uit te voeren.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Wijzigingen van uw lokale opslag plaats naar de server opslag plaats
Als u wijzigingen van uw lokale opslag plaats naar de server opslagplaats wilt pushen, moet u uw wijzigingen door voeren en vervolgens naar de server opslagplaats pushen. Als u uw wijzigingen wilt door voeren, opent u het git-opdracht programma, gaat u naar de map van uw lokale opslag plaats en geeft u de volgende opdrachten op.

```
git add --all
git commit -m "Description of your changes"
```

Voer de volgende opdracht uit om alle door voer te pushen naar de-server.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Wijzigingen in de service configuratie voor het API Management service-exemplaar implementeren

Zodra uw lokale wijzigingen zijn doorgevoerd en naar de server opslagplaats zijn gepusht, kunt u deze implementeren in uw API Management service-exemplaar.

Zie [Git-wijzigingen in de configuratie database implementeren met behulp](/rest/api/apimanagement/2019-12-01/tenantconfiguration)van de rest API voor meer informatie over het uitvoeren van deze bewerking met behulp van de rest API.

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Verwijzing naar bestands-en mapstructuur van lokale Git-opslag plaats

De bestanden en mappen in de lokale Git-opslag plaats bevatten de configuratie-informatie over het service-exemplaar.

| Item | Beschrijving |
| --- | --- |
| hoofdmap-API-beheer map |Bevat configuratie op het hoogste niveau voor het service-exemplaar |
| de map api's |Bevat de configuratie voor de api's in het service-exemplaar |
| map groepen |Bevat de configuratie voor de groepen in het service-exemplaar |
| map beleid |Bevat de beleids regels in het service-exemplaar |
| map portalStyles |Bevat de configuratie voor de aanpassingen van de ontwikkelaars Portal in het service-exemplaar |
| de map Products |Bevat de configuratie voor de producten in het service-exemplaar |
| map met sjablonen |Bevat de configuratie voor de e-mail sjablonen in het service-exemplaar |

Elke map kan een of meer bestanden bevatten, en in sommige gevallen een of meer mappen, bijvoorbeeld een map voor elke API, product of groep. De bestanden in elke map zijn specifiek voor het entiteits type dat wordt beschreven door de naam van de map.

| Bestands type | Doel |
| --- | --- |
| json |Configuratie-informatie over de respectieve entiteit |
| html |Beschrijvingen van de entiteit, vaak weer gegeven in de ontwikkelaars Portal |
| xml |Beleidsinstructies |
| css |Opmaak modellen voor het aanpassen van de ontwikkelaars Portal |

U kunt deze bestanden maken, verwijderen, bewerken en beheren op uw lokale bestands systeem, en de wijzigingen worden weer geïmplementeerd in uw API Management service-exemplaar.

> [!NOTE]
> De volgende entiteiten bevinden zich niet in de Git-opslag plaats en kunnen niet worden geconfigureerd met behulp van Git.
>
> * [Gebruikers](/rest/api/apimanagement/2019-12-01/user)
> * [Abonnementen](/rest/api/apimanagement/2019-12-01/subscription)
> * Naamwaarden
> * Portal-entiteiten voor ontwikkel aars behalve stijlen
>

### <a name="root-api-management-folder"></a>Hoofdmap-API-beheer map
De hoofdmap `api-management` bevat een `configuration.json` bestand dat informatie bevat over het hoogste niveau van het service-exemplaar in de volgende indeling.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

De eerste vier instellingen ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` en `UserRegistrationTermsConsentRequired` ) worden toegewezen aan de volgende instellingen op het tabblad **identiteiten** in het gedeelte **ontwikkelaars Portal** .

| Identiteits instelling | Wordt toegewezen aan |
| --- | --- |
| RegistrationEnabled |Aanwezigheid van ID-provider van **gebruikers naam en wacht woord** |
| UserRegistrationTerms |**Gebruiksvoorwaarden op het tekstvak voor de aanmelding van de gebruiker** |
| UserRegistrationTermsEnabled |Selectie vakje **gebruiks voorwaarden weer geven op de aanmeldings pagina** |
| UserRegistrationTermsConsentRequired |Selectie vakje **toestemming vereisen** |
| RequireUserSigninEnabled |Selectie vakje **anonieme gebruikers omleiden naar aanmeldings pagina** |

De volgende vier instellingen ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` en `DelegationValidationKey` ) worden toegewezen aan de volgende instellingen op het tabblad **delegering** in het gedeelte **ontwikkelaars Portal** .

| Overdrachts instelling | Wordt toegewezen aan |
| --- | --- |
| DelegationEnabled |Selectie vakje **aanmelden & aanmelding voor gemachtigde** |
| DelegationUrl |Tekstvak voor **eind punt-URL van overdracht** |
| DelegatedSubscriptionEnabled |Selectie vakje voor **delegeren van product abonnement** |
| DelegationValidationKey |Tekstvak voor **validatie sleutel voor gemachtigde** |

De laatste instelling, `$ref-policy` , verwijst naar het bestand met globale beleids overzichten voor het service-exemplaar.

### <a name="apis-folder"></a>de map api's
De `apis` map bevat een map voor elke API in het service-exemplaar, die de volgende items bevat.

* `apis\<api name>\configuration.json` -Dit is de configuratie voor de API en bevat informatie over de URL van de back-end-service en de bewerkingen. Dit is dezelfde informatie die zou worden geretourneerd als u [een specifieke API](/rest/api/apimanagement/2019-12-01/apis/get) met in-indeling moet aanroepen `export=true` `application/json` .
* `apis\<api name>\api.description.html` -Dit is de beschrijving van de API en komt overeen met de `description` eigenschap van de [API-entiteit](/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\` -deze map bevat `<operation name>.description.html` bestanden die zijn toegewezen aan de bewerkingen in de API. Elk bestand bevat de beschrijving van één bewerking in de API, die wordt toegewezen aan de `description` eigenschap van de [entiteit](/rest/api/visualstudio/operations/list#operationproperties) van de bewerking in de rest API.

### <a name="groups-folder"></a>map groepen
De `groups` map bevat een map voor elke groep die is gedefinieerd in het service-exemplaar.

* `groups\<group name>\configuration.json` : dit is de configuratie voor de groep. Dit is dezelfde informatie die zou worden geretourneerd als u de bewerking [een specifieke groep ophalen](/rest/api/apimanagement/2019-12-01/group/get) aanroept.
* `groups\<group name>\description.html` -Dit is de beschrijving van de groep en komt overeen met de `description` eigenschap van de [entiteit groep](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>map beleid
De `policies` map bevat de beleids instructies voor uw service-exemplaar.

* `policies\global.xml` -bevat beleids regels die zijn gedefinieerd in het globale bereik voor uw service-exemplaar.
* `policies\apis\<api name>\` -Als u beleids regels hebt gedefinieerd in het API-bereik, zijn deze opgenomen in deze map.
* `policies\apis\<api name>\<operation name>\` map: als er beleids regels zijn gedefinieerd in het bewerkings bereik, worden deze opgenomen in deze map in `<operation name>.xml` bestanden die worden toegewezen aan de beleids instructies voor elke bewerking.
* `policies\products\` -Als u beleids regels hebt gedefinieerd op product bereik, zijn deze opgenomen in deze map, die `<product name>.xml` bestanden bevat die zijn gekoppeld aan de beleids instructies voor elk product.

### <a name="portalstyles-folder"></a>map portalStyles
De `portalStyles` map bevat configuratie-en stijl bladen voor aanpassingen van de ontwikkelaars portal voor het service-exemplaar.

* `portalStyles\configuration.json` -bevat de namen van de opmaak modellen die worden gebruikt door de ontwikkelaars Portal
* `portalStyles\<style name>.css` -elk `<style name>.css` bestand bevat stijlen voor de ontwikkelaars Portal ( `Preview.css` en is `Production.css` standaard).

### <a name="products-folder"></a>de map Products
De `products` map bevat een map voor elk product dat is gedefinieerd in het service-exemplaar.

* `products\<product name>\configuration.json` : dit is de configuratie voor het product. Dit is dezelfde informatie die zou worden geretourneerd als u de bewerking [een specifieke product ophalen](/rest/api/apimanagement/2019-12-01/product/get) aanroept.
* `products\<product name>\product.description.html` : dit is de beschrijving van het product en komt overeen met de `description` eigenschap van de [product entiteit](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) in de rest API.

### <a name="templates"></a>sjablonen
De `templates` map bevat configuratie voor de [e-mail sjablonen](api-management-howto-configure-notifications.md) van het service-exemplaar.

* `<template name>\configuration.json` -Dit is de configuratie voor de e-mail sjabloon.
* `<template name>\body.html` : dit is de hoofd tekst van de e-mail sjabloon.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over andere manieren om uw service-exemplaar te beheren:

* Uw service-exemplaar beheren met de volgende Power shell-cmdlets
  * [Referentiemateriaal voor PowerShell-cmdlets voor service-implementatie](/powershell/module/wds)
  * [Referentiemateriaal voor PowerShell-cmdlets voor servicebeheer](/powershell/azure/servicemanagement/overview)
* Beheer uw service-exemplaar met behulp van de REST API
  * [Naslag informatie over API Management REST API](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
