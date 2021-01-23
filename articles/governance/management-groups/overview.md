---
title: Uw resources organiseren met beheergroepen - Azure Governance
description: Informatie over de managementgroepen, hoe hun machtigingen werken en hoe u ze gebruikt.
ms.date: 01/22/2021
ms.topic: overview
ms.custom: contperf-fy21q1
ms.openlocfilehash: e86501527ff68319fc8d2e942e7ffa977dcecbe6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736319"
---
# <a name="what-are-azure-management-groups"></a>Wat zijn Azure-beheergroepen?

Als uw organisatie veel abonnementen heeft, moet u de toegang, beleidsregels en naleving voor deze abonnementen op een efficiënte manier kunnen beheren. Azure-beheergroepen bieden een scopeniveau boven abonnementen. U ordent abonnementen in containers, zogenaamde 'beheergroepen', en past uw governancevoorwaarden hierop toe. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast. Beheergroepen bieden u beheer van bedrijfskwaliteit op grote schaal, ongeacht de typen abonnementen die u hebt.
Alle abonnementen binnen een beheergroep moeten afhankelijk zijn van dezelfde Azure Active Directory-tenant.

U kunt bijvoorbeeld beleid toepassen op een beheergroep, dat het aantal regio's beperkt dat beschikbaar is voor het maken van virtuele machines (VM’s). Dit beleid wordt dan toegepast op alle beheergroepen, abonnementen en resources onder die beheergroep, doordat het ervoor zorgt dat er alleen VM’s kunnen worden gemaakt in die regio.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiërarchie van managementgroepen en abonnementen

U kunt een flexibele structuur van managementgroepen en abonnementen bouwen om uw resources in een hiërarchie te ordenen voor uniform beleid en toegangsbeheer. Het volgende diagram laat een voorbeeld zien van hoe een hiërarchie voor governance kan worden gemaakt met behulp van beheergroepen.

:::image type="complex" source="./media/tree.png" alt-text="Diagram van een voorbeeld van een hiërarchie voor beheergroepen." border="false":::
   Diagram van een hoofdbeheergroep met zowel beheergroepen als abonnementen. Sommige onderliggende beheergroepen bevatten beheergroepen, sommige abonnementen en sommige beide. Een van de voorbeelden in de voorbeeldhiërarchie is vier niveaus van beheergroepen waarbij het onderliggende niveau alle abonnementen is.
:::image-end:::

U kunt een hiërarchie maken waarmee een beleid wordt toegepast, bijvoorbeeld om VM-locaties tot de regio US - west te beperken in de groep 'Productie'. Dit beleid is van toepassing op alle Enterprise Agreement-abonnementen die afgeleid zijn van die beheergroep en worden toegepast op alle VM's onder die abonnementen. Dit beveiligingsbeleid kan niet worden gewijzigd door de eigenaar van de resource of het abonnement, wat zorgt voor betere governance.

Een ander scenario waarbij u beheergroepen kunt gebruiken, is om gebruikers toegang te geven tot meerdere abonnementen. Als u meerdere abonnementen naar de desbetreffende beheergroep verplaatst, kunt u één [Azure-roltoewijzing](../../role-based-access-control/overview.md) in de beheergroep maken, die de toegang doorgeeft aan alle abonnementen. Eén toewijzing in de beheergroep kan gebruikers toegang geven tot alles wat ze nodig hebben. Er hoeven dan geen scripts te worden geschreven voor Azure RBAC-toewijzingen in meerdere abonnementen.

### <a name="important-facts-about-management-groups"></a>Belangrijke feiten over beheergroepen

- In één map kunnen 10.000 beheergroepen worden ondersteund.
- Een beheergroepstructuur ondersteunt tot wel zes niveaus.
  - Deze limiet is exclusief het hoofdniveau of het abonnementsniveau.
- Een beheergroep of abonnement biedt ondersteuning voor slechts één bovenliggend item.
- Elke beheergroep kan een groot aantal onderliggende items hebben.
- Alle abonnementen en beheergroepen bevinden zich in één hiërarchie in elke map. Zie [Belangrijke feiten over de hoofdbeheergroep](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Hoofdbeheergroep voor elke map

Elke map krijgt één beheergroep op het hoogste niveau, de 'hoofdbeheergroep'. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze hoofdbeheergroep kunt u algemene beleidsregels en Azure-roltoewijzingen toepassen op mapniveau. De globale beheerder van Azure AD moet eerst de rol Beheerder gebruikerstoegang van deze hoofdgroep [aan zichzelf toewijzen](../../role-based-access-control/elevate-access-global-admin.md). Nadat hij dit heeft gedaan, kan de beheerder een Azure-rol toewijzen aan andere directory-gebruikers of -groepen om de hiërarchie te beheren. Als beheerder kunt u uw eigen account toewijzen als eigenaar van de hoofdbeheergroep.

### <a name="important-facts-about-the-root-management-group"></a>Belangrijke feiten over de hoofdbeheergroep

- Standaard is de weergavenaam van de hoofdbeheergroep **Tenanthoofdgroep**. De id is de Azure Active Directory-id.
- Als u de weergavenaam wilt wijzigen, moet uw account de rol Eigenaar of Inzender in de hoofdbeheergroep hebben toegewezen gekregen. Zie [Change the name of a management group](manage.md#change-the-name-of-a-management-group) (De naam van een beheergroep wijzigen) om de naam van een beheergroep bij te werken.
- In tegenstelling tot andere beheergroepen kan de hoofdbeheergroep niet worden verplaatst of verwijderd.  
- Alle abonnementen en beheergroepen kunnen worden samengevouwen in deze ene hoofdbeheergroep binnen de map.
  - Alle resources in de map kunnen worden samengevouwen in de hoofdbeheergroep voor algemeen beheer.
  - Wanneer er een nieuw abonnement wordt gemaakt, wordt dit standaard automatisch in de hoofdbeheergroep geplaatst.
- Alle Azure-klanten kunnen de hoofdbeheergroep zien, maar niet alle klanten hebben de mogelijkheid om die hoofdbeheergroep te beheren.
  - Iedereen die toegang heeft tot een abonnement, kan de context zien van waar dat abonnement zich in de hiërarchie bevindt.  
  - Niemand krijgt standaard toegang tot de hoofdbeheergroep. Globale beheerders van Azure AD zijn de enige gebruikers die zichzelf toegang kunnen verschaffen. Wanneer ze toegang hebben tot de hoofdbeheergroep, kunnen de globale beheerders elke Azure-rol toewijzen aan andere gebruikers, zodat deze het beheer daarover hebben  
    .
- In SDK fungeert de hoofdbeheergroep, of tenanthoofdmap, als een beheergroep.

> [!IMPORTANT]
> Toewijzingen voor gebruikerstoegang of beleidstoewijzingen in de hoofdbeheergroep **zijn van toepassing op alle resources in de map**. Daarom moeten alle klanten goed nadenken over de noodzaak om items op dit niveau toe te wijzen. Op dit niveau mogen alleen toewijzingen voor gebruikerstoegang of beleid de aanduiding 'Must have' (Strikt noodzakelijk) hebben  
> .

## <a name="initial-setup-of-management-groups"></a>Eerste installatie van beheergroepen

Wanneer een gebruiker start met het gebruik van beheergroepen, vindt er een proces plaats voor de eerste installatie. Eerst wordt er een hoofdbeheergroep gemaakt in de map. Zodra deze groep klaar is, worden alle bestaande abonnementen die in de map zijn opgenomen gemaakt tot onderliggende items van de hoofdbeheergroep.
De reden dat dit proces zo werkt, is om er zeker van te zijn dat er binnen een map slechts één beheergroephiërarchie is. Die ene hiërarchie in de map zorgt ervoor dat klanten met een beheerdersrol globale toegang en beleidsregels kunnen toepassen waar andere klanten in de map niet omheen kunnen. Alle zaken die in de hoofdmap zijn toegewezen, zijn van toepassing op de hele hiërarchie, en dus op alle beheergroepen, abonnementen, resourcegroepen en resources binnen die Azure AD-tenant.

## <a name="trouble-seeing-all-subscriptions"></a>Problemen met de weergave van alle abonnementen

Bij een aantal mappen die vroeg in de preview (vóór 25 juni 2018) met beheergroepen begonnen te werken, deed zich een probleem voor waarbij niet alle abonnementen zich in de hiërarchie bevonden. Het proces voor het onderbrengen van alle abonnementen in de hiërarchie werden geïmplementeerd nadat er een rol- of beleidstoewijzing was uitgevoerd in de hoofdbeheergroep in de map.

### <a name="how-to-resolve-the-issue"></a>Het probleem oplossen

U kunt dit probleem op twee manieren verhelpen.

- Alle rol- en beleidstoewijzingen uit de hoofdbeheergroep verwijderen
  - Door alle beleids- en roltoewijzingen van de hoofdbeheergroep te verwijderen, zal de service alle abonnementen in de hiërarchie aanvullen tijdens de volgende nachtelijke cyclus. Dankzij dit proces wordt er niet onbedoeld toegang verleend of beleid toegewezen aan alle abonnementen van de tenant.
  - De beste manier om dit proces uit te voeren zonder uw services te beïnvloeden, is de rol- of beleidstoewijzingen één niveau onder de hoofdbeheergroep toe te passen. Vervolgens kunt u alle toewijzingen van het hoofdbereik verwijderen.
- De API rechtstreeks aanroepen om het backfill-proces te starten
  - Elke klant in de map kan de API's _TenantBackfillStatusRequest_ en _StartTenantBackfillRequest_ aanroepen. Wanneer de API StartTenantBackfillRequest wordt aangeroepen, wordt hiermee het initiële instellingsproces gestart voor het verplaatsen van alle abonnementen naar de hiërarchie. Met dit proces wordt ook afgedwongen dat alle nieuwe abonnementen een onderliggend element van de hoofdbeheergroep worden gemaakt.
    Dit proces kan worden uitgevoerd zonder de toewijzingen op het hoofdniveau te wijzigen. Door de API aan te roepen, geeft u aan dat een beleids- of toegangstoewijzing in de hoofdmap mag worden toegepast op alle abonnementen.

Als u vragen hebt over dit backfill-proces, neemt u contact op met: `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Toegang tot beheergroepen

Azure-beheergroepen ondersteunen [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../../role-based-access-control/overview.md) voor alle soorten toegang tot resources en roldefinities.
Deze machtigingen worden overgenomen door onderliggende resources die in de hiërarchie zijn opgenomen. Elke Azure-rol kan worden toegewezen aan een beheergroep die door alle onderliggende items in de hiërarchie voor de resources worden overgenomen. Zo kan de Azure-rol VM-inzender aan een beheergroep worden toegewezen. Deze rol heeft geen actie in de beheergroep, maar wordt overgenomen door alle VM's in die beheergroep.

In de volgende tabel staat een lijst met rollen en de acties die worden ondersteund in beheergroepen.

| Naam van Azure-rol             | Maken | Naam wijzigen | Verplaatsen\*\* | Verwijderen | Toegang toewijzen | Beleid toewijzen | Lezen  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Eigenaar                       | X      | X      | X        | X      | X             | X             | X     |
|Inzender                 | X      | X      | X        | X      |               |               | X     |
|Beheergroep-inzender\*            | X      | X      | X        | X      |               |               | X     |
|Lezer                      |        |        |          |        |               |               | X     |
|Beheergroep-lezer\*                 |        |        |          |        |               |               | X     |
|Inzender voor resourcebeleid |        |        |          |        |               | X             |       |
|Beheerder van gebruikerstoegang   |        |        |          |        | X             | X             |       |

\*: Met de rollen Beheergroep-inzender en Beheergroep-lezer kunnen gebruikers deze acties alleen uitvoeren op beheergroepniveau.  
\*\*: Bij roltoewijzingen hoeft er geen abonnement of beheergroep van of naar de hoofdbeheergroep te worden verplaatst. Zie [Uw resources beheren met beheergroepen](manage.md) voor meer informatie over het verplaatsen van items binnen de hiërarchie.

## <a name="azure-custom-role-definition-and-assignment"></a>Aangepaste roldefinitie en -toewijzing van Azure

Ondersteuning voor aangepaste rollen van Azure voor beheergroepen is momenteel in de preview-versie beschikbaar met enkele [beperkingen](#limitations). U kunt het bereik van de beheergroep definiëren in het toewijsbare bereik van de roldefinitie. Die aangepaste rol van Azure is dan beschikbaar voor toewijzing in die beheergroep en alle beheergroepen, abonnementen, resourcegroepen en resources daaronder. Deze aangepaste rol neemt machtigingen over in de hiërarchie op dezelfde manier als een ingebouwde rol.  

### <a name="example-definition"></a>Voorbeelddefinitie

[Het definiëren en maken van een aangepaste rol](../../role-based-access-control/custom-roles.md) verandert niet met het opnemen van beheergroepen. Gebruik het volledige pad om de beheergroep te definiëren: **/providers/Microsoft.Management/managementgroups/{groupId}** .

Gebruik de id van de beheergroep en niet de weergavenaam van de beheergroep. Deze fout wordt vaak gemaakt omdat beide aangepaste velden zijn bij het maken van een beheergroep.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemen met het verbreken van de roldefinitie en het toewijzingshiërarchiepad

Roldefinities kunnen overal binnen de hiërarchie van de beheergroep worden toegewezen. Een roldefinitie kan worden gedefinieerd in een bovenliggende beheergroep terwijl de daadwerkelijke roltoewijzing bestaat op het onderliggende abonnement. Omdat er een relatie bestaat tussen de twee items, ontvangt u een foutmelding wanneer u probeert de toewijzing van de definitie te scheiden.

Laten we bijvoorbeeld eens kijken naar een kleine sectie van een hiërarchie voor een besturingselement.

:::image type="complex" source="./media/subtree.png" alt-text="Diagram van een subset van het voorbeeld van een hiërarchie voor beheergroepen." border="false":::
   Het diagram is gericht op de hoofdbeheergroep met onderliggende IT- en Marketingbeheergroepen. De IT-beheergroep heeft één onderliggende beheergroep met de naam Productie terwijl de Marketingbeheergroep twee onderliggende abonnementen van de gratis proefversie heeft.
:::image-end:::

Stel dat er een aangepaste rol is gedefinieerd in de Marketing-beheergroep. Deze aangepaste rol wordt vervolgens toegewezen aan de twee gratis proefabonnementen.  

Als we een van deze subabonnementen proberen te verplaatsen naar een onderliggend niveau van de Production-beheergroep, wordt het pad van de abonnementsroltoewijzing naar de roldefinitie van de Marketing-beheergroep verbroken. In dit scenario wordt een foutbericht weergegeven waarin staat dat de verplaatsing niet is toegestaan omdat de relatie dan wordt verbroken.  

Er zijn verschillende opties om dit scenario op te lossen:
- Verwijder de roltoewijzing uit het abonnement voordat u het abonnement naar een nieuwe bovenliggende beheergroep verplaatst.
- Voeg het abonnement toe aan het toewijsbare bereik van de roldefinitie.
- Wijzig het toewijsbare bereik in de roldefinitie. In het bovenstaande voorbeeld kunt u de toewijsbare bereiken bijwerken vanuit Marketing naar de hoofdbeheergroep, zodat de definitie kan worden bereikt door beide vertakkingen van de hiërarchie.  
- Maak een andere aangepaste rol die is gedefinieerd in de andere vertakking. Voor deze nieuwe rol moet de roltoewijzing ook worden gewijzigd in het abonnement.  

### <a name="limitations"></a>Beperkingen  

Er bestaan beperkingen wanneer u aangepaste rollen gebruikt voor beheergroepen. 

 - U kunt slechts één beheergroep definiëren in het toewijsbare bereik van een nieuwe rol. Deze beperking bestaat om het aantal situaties te verminderen waarin de koppeling tussen roldefinities en roltoewijzingen wordt verbroken. Deze situatie treedt op wanneer een abonnement of beheergroep met een roltoewijzing wordt verplaatst naar een andere bovenliggende site die niet over de roldefinitie beschikt.  
 - Resourceprovider-gegevensvlakacties kunnen niet worden gedefinieerd in de aangepaste rollen van een beheergroep. Deze beperking bestaat omdat er een latentieprobleem is met het bijwerken van de gegevensvlak-resourceproviders. Er wordt aan dit latentieprobleem gewerkt en deze acties worden uitgeschakeld in de roldefinitie om risico's te beperken.
 - De Azure Resource Manager valideert niet het bestaan van de beheergroep in het toewijsbare bereik van de roldefinitie. Als er een type fout of een onjuiste beheer groep-ID wordt vermeld, wordt de roldefinitie nog steeds gemaakt.
 - Roltoewijzing voor een rol met _dataActions_ wordt niet ondersteund. Maak in plaats daarvan de roltoewijzing in het abonnements bereik.

> [!IMPORTANT]
> Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads.
> Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="moving-management-groups-and-subscriptions"></a>Beheergroepen en abonnementen verplaatsen 

Als u een beheergroep of een abonnement wilt verplaatsen om het/deze een onderliggend item van een andere beheergroep te maken, moeten drie regels worden geëvalueerd als waar.

Als u de verplaatsing wilt uitvoeren, hebt u het volgende nodig: 

- Schrijfmachtigingen voor beheergroepen en roltoewijzingen voor het onderliggende abonnement of de beheergroep.
  - Voorbeeld van een ingebouwde rol **Eigenaar**
- Schrijftoegang voor de beheergroep op de beoogde bovenliggende beheergroep.
  - Voorbeeld van een ingebouwde rol: **Eigenaar**, **Inzender**, **Inzender beheergroep**
- Schrijftoegang voor de beheergroep op de bestaande bovenliggende beheergroep.
  - Voorbeeld van een ingebouwde rol: **Eigenaar**, **Inzender**, **Inzender beheergroep**

**Uitzondering**: Als de beoogde of bestaande bovenliggende beheergroep de hoofdbeheergroep is, zijn de machtigingsvereisten niet van toepassing. Omdat de hoofdbeheergroep de standaardplek voor alle nieuwe beheergroepen en abonnementen is, hebt u geen machtigingen nodig om een item te verplaatsen.

Als de rol Eigenaar van het abonnement wordt overgenomen van de huidige beheergroep, zijn de verplaatsingsdoelen beperkt. U kunt het abonnement alleen verplaatsen naar een andere beheergroep waarvan u de rol Eigenaar hebt. U kunt het niet verplaatsen naar een beheergroep waarvan u een Inzender bent, omdat u dan het eigendom van het abonnement kwijtraakt. Als u direct bent toegewezen aan de rol Eigenaar van het abonnement (niet overgenomen van de beheergroep), kunt u het verplaatsen naar een beheergroep waarvan u een Inzender bent.

## <a name="audit-management-groups-using-activity-logs"></a>Beheergroepen controleren met behulp van activiteitenlogboeken

Beheergroepen worden ondersteund door het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md). U kunt op dezelfde centrale locatie als andere Azure-resources zoeken naar alle gebeurtenissen die in een beheergroep plaatsvinden. Zo kunt u alle gewijzigde rol- of beleidstoewijzingen binnen een bepaalde beheergroep bekijken.

:::image type="content" source="./media/al-mg.png" alt-text="Schermopname van activiteitenlogboeken en bewerkingen met betrekking tot de geselecteerde beheergroep." border="false":::

Bij het uitvoeren van query's op beheergroepen buiten de Azure-portal, ziet het doelbereik voor beheergroepen er als volgt uit: **/ providers/Microsoft.Management/managementGroups/{yourMgID}** .

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheergroepen gaat u naar:

- [Beheergroepen maken om Azure-resources te ordenen](./create-management-group-portal.md)
- [Uw beheergroepen wijzigen, verwijderen of beheren](./manage.md)
- Bekijk opties voor [Uw resourcehiërarchie beveiligen](./how-to/protect-resource-hierarchy.md)