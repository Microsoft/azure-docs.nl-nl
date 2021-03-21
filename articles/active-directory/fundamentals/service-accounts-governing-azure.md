---
title: Het beheren van Azure Active Directory-service accounts
description: Principes en procedures voor het beheren van de levens cyclus van service accounts in Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f540ab40a14af09aa8667860286021f572eb6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587896"
---
# <a name="governing-azure-ad-service-accounts"></a>Azure AD-service accounts beheren

Er zijn drie typen service accounts in Azure Active Directory (Azure AD): [beheerde identiteiten](service-accounts-managed-identities.md), [service-principals](service-accounts-principal.md)en gebruikers accounts die worden gebruikt als service accounts. Wanneer u deze service accounts maakt voor automatisch gebruik, worden er machtigingen verleend voor toegang tot resources in Azure en Azure AD. Resources kunnen bestaan uit Microsoft 365 Services, SaaS-toepassingen (Software as a Service), aangepaste toepassingen, data bases, HR-systemen, enzovoort. Bij het beheren van Azure AD-service accounts houdt u rekening met het maken, de machtigingen en de levens cyclus van de beveiliging en continuïteit.

> [!IMPORTANT] 
> Het wordt niet aangeraden om gebruikers accounts als service accounts te gebruiken omdat ze inherent minder veilig zijn. Dit geldt ook voor on-premises service accounts die zijn gesynchroniseerd met Azure AD, omdat deze niet worden geconverteerd naar service-principals. In plaats daarvan raden we u aan om beheerde identiteiten of service-principals te gebruiken. Op dit moment is het gebruik van beleids regels voor voorwaardelijke toegang niet mogelijk met Service-principals, maar de functionaliteit is beschikbaar.


## <a name="plan-your-service-account"></a>Uw service account plannen

Voordat u een service account maakt of een toepassing registreert, documenteert u de sleutel gegevens van het service account. Als er informatie wordt gedocumenteerd, is het eenvoudiger om het account effectief te controleren en te beheren. Het is raadzaam om de volgende gegevens te verzamelen en deze op te volgen in uw gecentraliseerde configuratie beheer database (CMDB).

| Gegevens| Description| Details |
| - | - | - |
| Eigenaar| Gebruiker of groep die verantwoordelijk is voor het beheren en bewaken van het service account.| Richt de eigenaar in met de vereiste machtigingen om het account te bewaken en implementeer een manier om problemen te verhelpen. Het probleem kan worden opgelost door de eigenaar of via een aanvraag naar de oplossing. |
| Doel| Hoe het account wordt gebruikt.| Het service account toewijzen aan een specifieke service, toepassing of script. Vermijd het maken van service accounts voor meerdere gebruik. |
| Machtigingen (bereiken)| Verwachte set machtigingen.| Documenteer de resources waartoe deze toegang heeft en de machtigingen voor deze resources. |
| CMDB-koppeling| Koppeling naar de resources die u wilt openen en scripts waarin het service account wordt gebruikt.| Zorg ervoor dat u de bron-en script eigen aars documenteert, zodat u alle benodigde upstream-en downstream-effecten van wijzigingen kunt door geven. |
| Risico-evaluatie| Risico en impact van het bedrijf als het account zou moeten worden aangetast.| Gebruik deze informatie om het bereik van machtigingen te beperken en te bepalen wie toegang moet hebben tot de account gegevens. |
| Periode voor beoordeling| Het schema waarop het service account moet worden gecontroleerd door de eigenaar.| Gebruik deze om de communicatie en beoordelingen van de beoordeling te plannen. Documenteer wat er moet gebeuren als een controle niet wordt uitgevoerd op een specifieke tijd na de geplande beoordelings periode. |
| Dood| De verwachte maximale levens duur van het account.| Gebruik deze optie om de communicatie met de eigenaar te plannen en uiteindelijk de accounts te verwijderen. Stel waar mogelijk een verval datum voor referenties in, waarbij de referenties niet automatisch kunnen worden doorgevoerd. |
| Name| De standaard naam van het account| Maak een naamgevings schema voor alle service accounts, zodat u gemakkelijk kunt zoeken, sorteren en filteren op service accounts. |


## <a name="use-the-principle-of-least-privileges"></a>Het principe van minimale bevoegdheden gebruiken
Verleen het service account alleen de machtigingen die nodig zijn om de taken uit te voeren, en niet meer. Als een service account behoefte heeft aan machtigingen op hoog niveau, bijvoorbeeld een algemeen beheerders bevoegdheids niveau, evalueert u waarom en probeert u de benodigde machtigingen te verlagen.

U wordt aangeraden de volgende procedures te gebruiken voor service account privileges.

**Machtigingen**

* Wijs geen ingebouwde rollen toe aan service accounts. Gebruik in plaats daarvan het [OAuth2 machtigings subsidie model voor Microsoft Graph](/graph/api/resources/oauth2permissiongrant),

* Als aan de service-principal een bevoorrechte rol moet worden toegewezen, kunt u een [aangepaste rol](../roles/custom-create.md) met specifieke, vereiste privileges op een tijdgebonden manier toewijzen.

* Neem geen service accounts op als leden van groepen met verhoogde machtigingen. 

* [Gebruik Power shell voor het inventariseren van leden van geprivilegieerde rollen](/powershell/module/azuread/get-azureaddirectoryrolemember), zoals   
`Get-AzureADDirectoryRoleMember`en filtert u op object type "Service-Principal".

   of gebruik  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Gebruik OAuth 2,0-bereiken](../develop/v2-permissions-and-consent.md) om de functionaliteit te beperken die een service account kan gebruiken voor een bron.
* Service-principals en beheerde identiteiten kunnen OAuth 2,0-bereiken gebruiken in een gedelegeerde context die een aangemelde gebruiker of als service account in de toepassings context imiteert. In de context van de toepassing is aangemeld.

* Controleer de verzoeken om de scopes service accounts om te controleren of ze geschikt zijn. Bijvoorbeeld, als een account bezig is met het aanvragen van bestanden. ReadWrite. all, evalueren of het eigenlijk alleen file. Read. all heeft. Zie voor meer informatie over machtigingen verwijzen naar [Microsoft Graph machtigingen](/graph/permissions-reference).

* Zorg ervoor dat u de ontwikkelaar van de toepassing of API vertrouwt met de toegang die is aangevraagd voor uw resources.

**Duur**

* Beperk de referenties van het service account (client geheim, certificaat) tot een verwachte gebruiks periode.

* Periodiek controleert het gebruik en het doel van service accounts. Controleer of er beoordelingen worden uitgevoerd voordat het account wordt verlopen.

Wanneer u een duidelijk inzicht hebt in het doel, het bereik en de benodigde machtigingen, maakt u uw service account. 

[Beheerde identiteiten maken en gebruiken](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Service-principals maken en gebruiken](../develop/howto-create-service-principal-portal.md)

Gebruik waar mogelijk een beheerde identiteit. Als u een beheerde identiteit niet kunt gebruiken, gebruikt u een service-principal. Als u een Service-Principal niet kunt gebruiken en vervolgens alleen een Azure AD-gebruikers account gebruikt.

 

## <a name="build-a-lifecycle-process"></a>Een levenscyclus proces bouwen

Het beheren van de levens cyclus van een service account begint met het plannen en eindigt met de permanente verwijdering. 

In dit artikel wordt eerder het gedeelte planning en maken besproken. U moet ook controleren, machtigingen controleren, het permanente gebruik van het account bepalen en uiteindelijk de inrichting van het account ongedaan maken.

### <a name="monitor-service-accounts"></a>Service accounts bewaken

Proactief uw service accounts controleren om te controleren of de gebruiks patronen van het service account overeenkomen met de beoogde patronen en dat het service account nog actief wordt gebruikt.

**Verzamel en controleer de aanmeldingen voor service accounts met een van de volgende methoden:**

* Met de Azure AD-Sign-In-Logboeken in de Azure AD-Portal.

* Exporteren van de Azure AD-Sign-In logboeken naar [Azure Storage](../../storage/index.yml), [Azure Event hubs](../../event-hubs/index.yml)of [Azure monitor](../../azure-monitor/logs/data-platform-logs.md).


![Scherm opname met het aanmeldings scherm van de Service-Principal.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**In de Sign-In Logboeken vindt u de volgende informatie:**

* Zijn er service accounts die zich niet meer aanmelden bij de Tenant?

* Worden er aanmeldings patronen van service accounts gewijzigd?

We raden u aan om Azure AD-aanmeld logboeken te exporteren en te importeren in uw bestaande Security Information and Event Management-hulpprogram ma's (SIEM) zoals Azure Sentinel. Gebruik uw SIEM om waarschuwingen en dash boards te maken.

### <a name="review-service-account-permissions"></a>Machtigingen voor het service account controleren

Controleer regel matig de verleende machtigingen en bereiken die worden geopend door service accounts om te zien of ze kunnen worden gereduceerd.

* [Power shell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) gebruiken voor het [maken van Automation voor het controleren en documenteren van](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) bereiken waaraan toestemming wordt verleend voor een service account.

* Gebruik Power shell om de referenties van de [bestaande service-principals te controleren](https://github.com/AzureAD/AzureADAssessment) en de geldigheid ervan te controleren.

* Stel de referenties van de Service-Principal niet in op nooit verlopen.

* Gebruik waar mogelijk certificaten of referenties die zijn opgeslagen in azure-sleutel kluis.

Het gratis Power shell-voor beeld van micro soft verzamelt de OAuth2-subsidies en referentie gegevens van de Service-Principal, registreert deze in een bestand met door komma's gescheiden waarden (CSV) en een Power BI voorbeeld dashboard voor het interpreteren en gebruiken van de gegevens. Zie [AzureAD/AzureADAssessment: hulp programma voor het evalueren van een Azure AD-Tenant status en-configuratie (github.com)](https://github.com/AzureAD/AzureADAssessment) voor meer informatie.

### <a name="recertify-service-account-use"></a>Het gebruik van het service account opnieuw certificeren

Stel een beoordelings proces in om ervoor te zorgen dat service accounts regel matig worden gecontroleerd door hun eigen aren en de beveiliging of het IT-team met regel matige tussen pozen. 

**Het proces moet het volgende omvatten:**

* De controle cyclus van elke service accounts bepalen (moet worden beschreven in uw CMDB).

* De communicatie met eigenaar en beveiliging of IT-teams voordat beoordelingen worden gestart.

* De timing en de inhoud van waarschuwings berichten als de controle ontbreekt.

* Instructies over wat u moet doen als de eigen aren niet kunnen controleren of reageren. U kunt het account bijvoorbeeld uitschakelen (maar niet verwijderen) totdat de controle is voltooid.

* Instructies voor het bepalen van de upstream-en downstream-afhankelijkheden en het melden van andere resource-eigen aren van effecten.

**De beoordeling moet de eigenaar en hun IT-partner bevatten waarin wordt verklaard dat:**

* Het account is nog steeds nodig.

* De aan het account verleende machtigingen zijn voldoende en nood zakelijk, of er wordt een wijziging aangevraagd.

* De toegang tot het account en de bijbehorende referenties worden beheerd.

* De referenties die het account gebruikt, zijn geschikt voor het risico dat het account is geëvalueerd met (zowel referentie type als referentie-levens duur)

* De risico Score van het account is niet gewijzigd sinds de laatste hercertificering

* Een update voor de verwachte levens duur van het account en de volgende hercertificerings datum.

### <a name="deprovision-service-accounts"></a>Inrichting van service accounts ongedaan maken

* * De inrichting van service accounts ongedaan maken in de volgende omstandigheden: * * * *

* Het script of de toepassing waarvoor het service account is gemaakt, wordt buiten gebruik gesteld.

* De functie binnen het script of de toepassing waarvoor het service account wordt gebruikt (bijvoorbeeld toegang tot een specifieke resource) wordt buiten gebruik gesteld.


* Het service account wordt vervangen door een ander service account.

* De referenties zijn verlopen of het account is niet-functioneel en er zijn geen klachten.

**De processen voor het ongedaan maken van de inrichting moeten de volgende taken bevatten.**

1. Zodra de inrichting van de bijbehorende toepassing of het script is opheffen, controleert u de [aanmeldingen](../reports-monitoring/concept-sign-ins.md#sign-ins-report) en de toegang tot bronnen door het service account.

   * Als het account nog steeds actief is, bepaalt u hoe het wordt gebruikt voordat u de volgende stappen uitvoert.
 
2. Als dit een beheerde service-identiteit is, schakelt u het service account niet in voor aanmelden, maar verwijdert u het niet uit de map.

3. Rollen toewijzingen en OAuth2-toestemming subsidies voor het service account intrekken.

4. Na een bepaalde periode en een ruim waarschuwings niveau voor eigen aren verwijdert u het service account uit de map.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het beveiligen van Azure-service accounts:

[Inleiding tot Azure-service accounts](service-accounts-introduction-azure.md)

[Beheerde identiteiten beveiligen](service-accounts-managed-identities.md)

[Service principes beveiligen](service-accounts-principal.md)




 

