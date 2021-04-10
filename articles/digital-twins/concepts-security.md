---
title: Beveiliging voor Azure Digital Twins-oplossingen
titleSuffix: Azure Digital Twins
description: Meer informatie over best practices voor beveiliging met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730504"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Apparaatdubbels beveiligen

Voor de beveiliging biedt Azure Digital Apparaatdubbels nauw keurige toegangs controle over specifieke gegevens, resources en acties in uw implementatie. Dit wordt gedaan door middel van een gedetailleerde beheer strategie voor rollen en machtigingen die **Azure op rollen gebaseerd toegangs beheer (Azure RBAC)** wordt genoemd. Meer informatie over de algemene principes van Azure RBAC vindt u [hier](../role-based-access-control/overview.md).

Azure Digital Apparaatdubbels biedt ook ondersteuning voor versleuteling van gegevens in rust.

## <a name="roles-and-permissions-with-azure-rbac"></a>Rollen en machtigingen met Azure RBAC

Azure RBAC wordt geleverd aan Azure Digital Apparaatdubbels via integratie met [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

U kunt Azure RBAC gebruiken om machtigingen toe te kennen aan een *beveiligingsprincipal*, die een gebruiker, een groep of een Application Service-Principal kan zijn. De beveiligingsprincipal wordt geverifieerd door Azure AD en ontvangt een OAuth 2,0-token als resultaat. Dit token kan worden gebruikt voor het autoriseren van een toegangs aanvraag aan een Azure Digital Apparaatdubbels-exemplaar.

### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

In azure AD is Access een proces dat uit twee stappen bestaat. Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot Azure Digital Apparaatdubbels, moet de aanvraag worden *geverifieerd* en *geautoriseerd*. 

1. Eerst wordt de identiteit van de beveiligingsprincipal *geverifieerd* en wordt een OAuth 2,0-token geretourneerd.
2. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag voor de Azure Digital Apparaatdubbels-service om toegang tot de opgegeven bron te *autoriseren* .

Voor de verificatie stap moet elke toepassings aanvraag een OAuth 2,0-toegangs token tijdens runtime bevatten. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een [Azure functions](../azure-functions/functions-overview.md) -app, kan deze een **beheerde identiteit** gebruiken om toegang te krijgen tot de resources. Meer informatie over beheerde identiteiten vindt u in de volgende sectie.

De autorisatie stap vereist dat een Azure-rol wordt toegewezen aan de beveiligingsprincipal. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Azure Digital Apparaatdubbels biedt Azure-rollen die sets machtigingen voor Azure Digital Apparaatdubbels-resources omvatten. Deze rollen worden verderop in dit artikel beschreven.

Zie [*inzicht in de verschillende rollen*](../role-based-access-control/rbac-and-directory-admin-roles.md) in de documentatie van Azure RBAC voor meer informatie over rollen en roltoewijzingen die worden ondersteund in Azure.

#### <a name="authentication-with-managed-identities"></a>Verificatie met beheerde identiteiten

[Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) is een functie van meerdere Azure waarmee u een beveiligde identiteit kunt maken die is gekoppeld aan de implementatie waarin de toepassings code wordt uitgevoerd. U kunt deze identiteit vervolgens koppelen aan toegangs beheer rollen om aangepaste machtigingen te verlenen voor toegang tot specifieke Azure-resources die uw toepassing nodig heeft.

Met beheerde identiteiten beheert het Azure-platform deze runtime-identiteit. U hoeft geen toegangs sleutels op te slaan en te beveiligen in uw toepassings code of configuratie, hetzij voor de identiteit zelf, hetzij voor de resources die u nodig hebt. Een Azure Digital Apparaatdubbels-client-app die binnen een Azure App Service-toepassing wordt uitgevoerd, hoeft geen SAS-regels en sleutels of andere toegangs tokens te verwerken. De client-app heeft alleen het eindpunt adres van de Azure Digital Apparaatdubbels-naam ruimte nodig. Wanneer de app verbinding maakt, koppelt Azure Digital Apparaatdubbels de context van de beheerde entiteit aan de client. Zodra deze is gekoppeld aan een beheerde identiteit, kan uw Azure Digital Apparaatdubbels-client alle geautoriseerde bewerkingen uitvoeren. De autorisatie wordt vervolgens verleend door een beheerde entiteit te koppelen aan een Azure Digital Apparaatdubbels Azure-rol (hieronder beschreven).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorisatie: Azure-functies voor Azure Digital Apparaatdubbels

Azure biedt **twee ingebouwde rollen van Azure** voor het verlenen van toegang tot de Azure Digital apparaatdubbels [Data-vlak-api's](how-to-use-apis-sdks.md#overview-data-plane-apis). U kunt op naam of op ID verwijzen naar de rollen:

| Ingebouwde rol | Beschrijving | Id | 
| --- | --- | --- |
| Azure Digital Apparaatdubbels-gegevens eigenaar | Biedt volledige toegang tot Azure Digital Apparaatdubbels-bronnen | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digital Apparaatdubbels-gegevens lezer | Geeft alleen-lezen toegang tot Azure Digital Apparaatdubbels-resources | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

U kunt rollen op twee manieren toewijzen:
* via het deel venster toegangs beheer (IAM) voor Azure Digital Apparaatdubbels in de Azure Portal (Zie [*Azure-rollen toewijzen met behulp van de Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* via CLI-opdrachten om een rol toe te voegen of te verwijderen

Voor meer gedetailleerde stappen over hoe u dit doet, kunt u het uitproberen in de zelf studie over Azure Digital Apparaatdubbels [*: verbinding maken met een end-to-end oplossing*](tutorial-end-to-end.md).

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd [*begrijpen functie definities*](../role-based-access-control/role-definitions.md) in de documentatie van Azure RBAC. Zie [*aangepaste rollen in azure*](../role-based-access-control/custom-roles.md)voor meer informatie over het maken van aangepaste Azure-rollen.

##### <a name="automating-roles"></a>Rollen automatiseren

Wanneer u verwijst naar rollen in geautomatiseerde scenario's, is het raadzaam om deze te laten verwijzen door hun **id's** in plaats van hun namen. De namen kunnen veranderen tussen releases, maar de Id's worden niet weer geven, waardoor ze een stabielere referentie hebben in Automation.

> [!TIP]
> Als u rollen hebt assiging met een cmdlet, zoals `New-AzRoleAssignment` ([verwijzing](/powershell/module/az.resources/new-azroleassignment)), kunt u de `-RoleDefinitionId` para meter gebruiken in plaats van een `-RoleDefinitionName` id door te geven in plaats van een naam voor de rol.

### <a name="permission-scopes"></a>Machtigingsbereiken

Voordat u een Azure-rol toewijst een beveiligingsprincipal, moet u het toegangsbereik bepalen dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Azure Digital Apparaatdubbels-resources kunt bereiken.
* Modellen: met de acties voor deze resource bepaalt u de controle over [modellen](concepts-models.md) die zijn geüpload in azure Digital apparaatdubbels.
* Query Digital Apparaatdubbels Graph: de acties voor deze resource bepalen de mogelijkheid om [query bewerkingen](concepts-query-language.md) uit te voeren op digitale apparaatdubbels in de Azure Digital apparaatdubbels Graph.
* Digitale dubbele: de acties voor deze resource bieden controle over ruwe bewerkingen op [digitale apparaatdubbels](concepts-twins-graph.md) in de dubbele grafiek.
* Digitale dubbele relatie: de acties voor deze bron bepalen de controle over ruwe bewerkingen op [relaties](concepts-twins-graph.md) tussen digitale apparaatdubbels in het dubbele diagram.
* Gebeurtenis route: de acties voor deze bron bepalen de machtigingen voor het [routeren van gebeurtenissen](concepts-route-events.md) van Azure Digital apparaatdubbels naar een eindpunt service, zoals [Event hub](../event-hubs/event-hubs-about.md), [Event grid](../event-grid/overview.md)of [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Machtigingen voor probleem oplossing

Als een gebruiker probeert een actie uit te voeren die niet is toegestaan door hun rol, wordt mogelijk een fout bericht weer gegeven bij het lezen van de service aanvraag `403 (Forbidden)` . Zie [*probleem oplossing: Azure Digital apparaatdubbels-aanvraag is mislukt met status: 403 (verboden)*](troubleshoot-error-403.md)voor meer informatie en stappen voor probleem oplossing.

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Beheerde identiteit voor toegang tot andere resources (preview)

Door het instellen van een door [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **beheerde identiteit** voor een Azure Digital apparaatdubbels-exemplaar kan het exemplaar eenvoudig toegang krijgen tot andere met Azure AD beveiligde resources, zoals [Azure Key Vault](../key-vault/general/overview.md). De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in te richten of te draaien. Zie  [*beheerde identiteiten voor Azure-resources*](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in azure AD. 

Azure ondersteunt twee typen beheerde identiteiten: systeem toegewezen en door de gebruiker toegewezen. Azure Digital Apparaatdubbels ondersteunt momenteel alleen door **het systeem toegewezen identiteiten**. 

U kunt een door het systeem toegewezen beheerde identiteit voor uw Azure Digital-instantie gebruiken om te verifiëren bij een [aangepast eind punt](concepts-route-events.md#create-an-endpoint). Azure Digital Apparaatdubbels ondersteunt door het systeem toegewezen verificatie op basis van een id voor eind punten voor [Event hub](../event-hubs/event-hubs-about.md) en [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   doelen, en naar een [Azure storage container](../storage/blobs/storage-blobs-introduction.md)   eindpunt voor gebeurtenissen met een [onbestelbare letter](concepts-route-events.md#dead-letter-events). [Event grid](../event-grid/overview.md)   eind punten worden momenteel niet ondersteund voor beheerde identiteiten.

Zie [*How-to: een beheerde identiteit inschakelen voor routerings gebeurtenissen (preview)*](./how-to-enable-managed-identities-portal.md)voor instructies over het inschakelen van een door een systeem beheerde identiteit voor Azure Digital apparaatdubbels en het gebruik ervan om gebeurtenissen te routeren.

## <a name="private-network-access-with-azure-private-link-preview"></a>Toegang tot privé netwerk met persoonlijke Azure-koppeling (preview-versie)

[Persoonlijke Azure-koppeling](../private-link/private-link-overview.md) is een service waarmee u toegang hebt tot Azure-resources (zoals [Azure Event hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md)en [Azure Cosmos DB](../cosmos-db/introduction.md)) en door Azure gehoste klanten-en partner services via een persoonlijk eind punt in uw [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md). 

Op dezelfde manier kunt u privé-eind punten gebruiken voor uw Azure Digital dubbele instantie zodat clients die zich in uw virtuele netwerk bevinden, veilig toegang hebben tot het exemplaar via de persoonlijke koppeling. 

Het persoonlijke eind punt gebruikt een IP-adres uit uw Azure VNet-adres ruimte. Netwerk verkeer tussen een client op uw particuliere netwerk en het Azure Digital Apparaatdubbels-exemplaar passeren over het VNet en een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling aan het open bare Internet wordt geëlimineerd. Dit is een visuele representatie van dit systeem:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Een diagram met een netwerk voor een PowerGrid-bedrijf dat een beveiligd VNET is zonder toegang tot Internet/open bare Clouds, verbinding maken via een persoonlijke koppeling naar een Azure Digital Apparaatdubbels-instantie met de naam CityOfTwins.":::

Als u een persoonlijk eind punt voor uw Azure Digital Apparaatdubbels-exemplaar configureert, kunt u uw Azure Digital Apparaatdubbels-exemplaar beveiligen en de open bare bloot stelling elimineren, en gegevens exfiltration van uw VNet voor komen.

Voor instructies over het instellen van een persoonlijke koppeling voor Azure Digital Apparaatdubbels raadpleegt u [*How to: persoonlijke toegang inschakelen met persoonlijke koppeling (preview)*](./how-to-enable-private-link-portal.md).

### <a name="design-considerations"></a>Overwegingen bij het ontwerpen 

Wanneer u werkt met een privé koppeling voor Azure Digital Apparaatdubbels, kunt u het volgende overwegen:
* **Prijzen**: Zie  [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link)voor prijs informatie. 
* **Regionale Beschik baarheid**: voor Azure Digital apparaatdubbels is deze functie beschikbaar in alle Azure-regio's waar Azure Digital apparaatdubbels beschikbaar is. 
* **Maximum aantal privé-eind punten per Azure Digital apparaatdubbels-exemplaar**: 10

Zie voor meer informatie over de limieten van privé koppeling [Azure-documentatie voor persoonlijke koppelingen: beperkingen](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Servicetags

Een **servicetag** vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd. Zie voor meer informatie over service tags  [*virtuele netwerk Tags*](../virtual-network/service-tags-overview.md). 

U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules)   of [Azure firewall](../firewall/service-tags.md)door service tags te gebruiken in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de servicetag (in dit geval **AzureDigitalTwins**) op te geven in het juiste *bron*-   of *doel*   veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. 

Hieronder vindt u de details van de **AzureDigitalTwins** -servicetag.

| Tag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Opmerking: deze tag of de IP-adressen waarop deze tag wordt toegepast, kunnen worden gebruikt om de toegang te beperken tot eind punten die zijn geconfigureerd voor [gebeurtenis routes](concepts-route-events.md). | Inkomend | Nee | Ja |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Service tags gebruiken voor het openen van gebeurtenis route-eind punten 

Hier volgen de stappen voor het openen van [gebeurtenis route](concepts-route-events.md) -eind punten met behulp van service tags met Azure Digital apparaatdubbels.

1. Down load eerst de verwijzing naar het JSON-bestand met de IP-bereiken en service tags van Azure: [*Azure IP-bereiken en service Tags*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Zoek naar IP-adresbereiken ' AzureDigitalTwins ' in het JSON-bestand.  

3. Raadpleeg de documentatie van de externe resource die is verbonden met het eind punt (bijvoorbeeld de [Event grid](../event-grid/overview.md), [Event hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)of [Azure Storage](../storage/blobs/storage-blobs-overview.md) voor [onbestelbare gebeurtenissen](concepts-route-events.md#dead-letter-events)) om te zien hoe u IP-filters voor die bron kunt instellen.

4. Stel IP-filters in op de externe bron (nen) met behulp van het IP-bereik uit *stap 2*.  

5. Werk de IP-bereiken zo nodig regel matig bij. De bereiken kunnen na verloop van tijd veranderen, dus het is een goed idee ze regel matig te controleren en ze te vernieuwen wanneer dat nodig is. De frequentie van deze updates kan verschillen, maar het is een goed idee om ze eenmaal per week te controleren.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest

Azure Digital Apparaatdubbels zorgt voor versleuteling van gegevens in rust en in transit als deze zijn geschreven in onze data centers en ontsleutelt deze voor u wanneer u er toegang toe hebt. Deze versleuteling vindt plaats met behulp van een door micro soft beheerde versleutelings sleutel.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Cross-Origin Resource Sharing)

Azure Digital Apparaatdubbels ondersteunt momenteel geen **CORS (cross-Origin Resource Sharing)**. Als u dus een REST API aanroept vanuit een browser-app, een API Management-interface [(APIM)](../api-management/api-management-key-concepts.md) of een [Power apps](/powerapps/powerapps-overview) -connector, wordt er mogelijk een beleids fout weer geven.

U kunt deze fout oplossen door een van de volgende acties uit te voeren:
* De CORS-header `Access-Control-Allow-Origin` van het bericht verwijderen. Deze header geeft aan of het antwoord kan worden gedeeld. 
* U kunt ook een CORS-proxy maken en de Azure Digital Apparaatdubbels REST API aanvraag door voeren. 

## <a name="next-steps"></a>Volgende stappen

* Zie de volgende concepten in actie [*: een exemplaar en authenticatie instellen*](how-to-set-up-instance-portal.md).

* Bekijk hoe u met deze concepten communiceert vanuit de code van de client toepassing in [*How-to: app-verificatie code schrijven*](how-to-authenticate-client.md).

* Meer informatie over [Azure RBAC](../role-based-access-control/overview.md).