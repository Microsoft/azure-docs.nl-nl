---
title: DNS-alias
description: Uw toepassingen kunnen verbinding maken met een alias voor de naam van de server voor Azure SQL Database. In de tussentijd kunt u de SQL Database de alias naar op elk gewenst moment wijzigt, om testen te vergemakkelijken, en meer.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376287"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias voor Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database heeft een Domain Name System server (DNS). PowerShell- en REST API's accepteren aanroepen voor het maken en beheren van [DNS-aliassen](#anchor-powershell-code-62x) voor de [naam van uw logische SQL-server.](logical-servers.md)

Een *DNS-alias* kan worden gebruikt in plaats van de servernaam. Clientprogramma's kunnen de alias gebruiken in hun verbindingsreeksen. De DNS-alias biedt een vertaallaag die uw clientprogramma's kan omleiden naar verschillende servers. Met deze laag hebt u geen moeite om alle clients en hun verbindingsreeksen te zoeken en te bewerken.

Veelvoorkomende toepassingen voor een DNS-alias zijn onder andere de volgende gevallen:

- Maak een gemakkelijk te onthouden naam voor een server.
- Tijdens de eerste ontwikkeling kan uw alias verwijzen naar een testserver. Wanneer de toepassing live gaat, kunt u de alias wijzigen zodat deze naar de productieserver verwijst. Voor de overgang van test naar productie zijn geen wijzigingen vereist in de configuraties van verschillende clients die verbinding maken met de server.
- Stel dat de enige database in uw toepassing wordt verplaatst naar een andere server. U kunt de alias wijzigen zonder dat u de configuraties van verschillende clients moet wijzigen.
- Tijdens een regionale storing gebruikt u geo-herstel om uw database op een andere server en regio te herstellen. U kunt uw bestaande alias wijzigen zodat deze naar de nieuwe server kan wijzen, zodat de bestaande clienttoepassing er opnieuw verbinding mee kan maken.

## <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) van internet

Internet is afhankelijk van de DNS. De DNS zet uw gebruikersvriendelijke namen om in de naam van uw server.

## <a name="scenarios-with-one-dns-alias"></a>Scenario's met één DNS-alias

Stel dat u uw systeem moet overschakelen naar een nieuwe server. In het verleden moest u elke connection string in elk clientprogramma zoeken en bijwerken. Maar als de verbindingsreeksen nu een DNS-alias gebruiken, moet alleen een alias-eigenschap worden bijgewerkt.

De FUNCTIE DNS-alias van Azure SQL Database kan helpen in de volgende scenario's:

### <a name="test-to-production"></a>Testen naar productie

Wanneer u begint met het ontwikkelen van de clientprogramma's, moet u ze een DNS-alias laten gebruiken in hun verbindingsreeksen. U maakt dat de eigenschappen van de alias naar een testversie van uw server wijzen.

Later wanneer het nieuwe systeem in productie gaat, kunt u de eigenschappen van de alias bijwerken zodat deze naar de productieserver wijzen. Er is geen wijziging in de clientprogramma's nodig.

### <a name="cross-region-support"></a>Ondersteuning voor regio-overschrijdende regio's

Een noodherstel kan uw server verplaatsen naar een andere geografische regio. Voor een systeem dat een DNS-alias gebruikt, kan de noodzaak om alle verbindingsreeksen voor alle clients te zoeken en bij te werken worden vermeden. In plaats daarvan kunt u een alias bijwerken om te verwijzen naar de nieuwe server die nu als host voor uw Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>Eigenschappen van een DNS-alias

De volgende eigenschappen zijn van toepassing op elke DNS-alias voor uw server:

- *Unieke naam:* Elke aliasnaam die u maakt, is uniek voor alle servers, net zoals servernamen.
- *Server is vereist:* Een DNS-alias kan niet worden gemaakt, tenzij deze precies naar één server verwijst en de server al moet bestaan. Een bijgewerkte alias moet altijd verwijzen naar exact één bestaande server.
  - Wanneer u een server neer zet, worden ook alle DNS-aliassen die naar de server verwijzen, door het Azure-systeem verwijderd.
- *Niet gebonden aan een regio:* DNS-aliassen zijn niet gebonden aan een regio. DNS-aliassen kunnen worden bijgewerkt om te verwijzen naar een server die zich in een geografische regio bevindt.
  - Bij het bijwerken van een alias om naar een andere server te verwijzen, moeten beide servers echter bestaan in hetzelfde *Azure-abonnement.*
- *Machtigingen:* Als u een DNS-alias wilt beheren, moet de gebruiker *machtigingen voor Inzender* voor servers of hoger hebben. Zie Aan de slag met op rollen gebaseerd toegangsbeheer van [Azure in de Azure Portal.](../../role-based-access-control/overview.md)

## <a name="manage-your-dns-aliases"></a>Uw DNS-aliassen beheren

Zowel REST API's als PowerShell-cmdlets zijn beschikbaar waarmee u uw DNS-aliassen programmatisch kunt beheren.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API's voor het beheren van uw DNS-aliassen

De documentatie voor de REST API's is beschikbaar in de buurt van de volgende weblocatie:

- [Azure SQL Database REST API](/rest/api/sql/)

De REST API's zijn ook te zien in GitHub op:

- [Azure SQL Database REST API's voor DNS-aliassen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell voor het beheren van uw DNS-aliassen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Er zijn PowerShell-cmdlets beschikbaar die de REST API's aanroepen.

Een codevoorbeeld van PowerShell-cmdlets die worden gebruikt voor het beheren van DNS-aliassen wordt beschreven op:

- [PowerShell voor DNS-alias voor Azure SQL Database](dns-alias-powershell-create.md)

De cmdlets die in het codevoorbeeld worden gebruikt, zijn als volgt:

- [New-AzSqlServerDnsAlias:](/powershell/module/az.Sql/New-azSqlServerDnsAlias)hiermee maakt u een nieuwe DNS-alias in het Azure SQL Database servicesysteem. De alias verwijst naar server 1.
- [Get-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Get-azSqlServerDnsAlias)haal alle DNS-aliassen op die zijn toegewezen aan server 1 en vermeld deze.
- [Set-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Set-azSqlServerDnsAlias)hiermee wijzigt u de servernaam waar de alias naar is geconfigureerd, van server 1 naar server 2.
- [Remove-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)verwijder de DNS-alias van server 2 met behulp van de naam van de alias.

## <a name="limitations"></a>Beperkingen

Op dit moment heeft een DNS-alias de volgende beperkingen:

- *Vertraging van maximaal 2 minuten:* Het duurt maximaal twee minuten voordat een DNS-alias is bijgewerkt of verwijderd.
  - Ongeacht een korte vertraging, stopt de alias onmiddellijk met het verwijzen van clientverbindingen naar de verouderde server.
- *DNS-zoekactie:* Op dit moment is de enige gezaghebbende manier om te controleren naar welke server een bepaalde DNS-alias verwijst, door een [DNS-zoekactie uit te voeren.](/windows-server/administration/windows-commands/nslookup)
- _Tabelcontrole wordt niet ondersteund:_ U kunt geen DNS-alias gebruiken op een server met *tabelcontrole* ingeschakeld voor een database.
  - Tabelcontrole is afgeschaft.
  - We raden u aan om over te gaan op [Blob Auditing.](../../azure-sql/database/auditing-overview.md)

## <a name="related-resources"></a>Gerelateerde resources

- [Overzicht van bedrijfscontinuïteit met Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md), inclusief herstel na noodherstel.
- [Azure REST API-naslaginformatie](/rest/api/azure/)
- [SERVER DNS-aliassen-API](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Volgende stappen

- [PowerShell voor DNS-alias voor Azure SQL Database](dns-alias-powershell-create.md)
