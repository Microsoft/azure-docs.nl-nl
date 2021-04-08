---
title: Veelgestelde vragen over ClearDB MySql-data bases met Azure App Service
description: Antwoorden op veelgestelde vragen over het gebruik van ClearDB MySQL-data bases met Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599793"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Veelgestelde vragen over ClearDB MySql-data bases met Azure App Service
Deze veelgestelde vragen antwoorden op veelgestelde vragen over het gebruik en het kopen van ClearDB MySQL-data bases voor Azure Web Apps.

> [!IMPORTANT]
> Vanaf 13 juni 2018 werden door micro soft geClearDBeerde Azure-klanten die momenteel worden gefactureerd voor een direct facturerings model met ClearDB. De informatie in dit artikel is nu verouderd. Het is niet meer mogelijk om een ClearDB-data base te maken of te upgraden die is gemaakt in Azure.
>
> Zie [wijzigingen in ClearDB-service plannen](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)voor meer informatie en de volgende stappen.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Welke opties heb ik voor MySQL op Azure?
Zie [ClearDB](https://w2.cleardb.net/) voor de meest recente informatie over die service. ClearDB is een MySQL-hosting service en beheert de MySQL-infra structuur voor u. 

U hebt verschillende andere opties voor het hosten van MySQL in Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [MySQL-cluster dat wordt uitgevoerd op een virtuele Azure-machine](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Eén exemplaar van MySQL dat wordt uitgevoerd op een virtuele Azure-machine](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Heb ik een credit card nodig voor de web-app + MySQL-sjabloon in de Azure Marketplace?
Dit is afhankelijk van het type abonnement dat u gebruikt. Hier volgen enkele veelgebruikte abonnements typen:

* [Betalen](https://azure.microsoft.com/offers/ms-azr-0003p/)naar gebruik: er is een credit card vereist en wanneer u een betaalde MySQL-data base koopt, wordt uw credit card in rekening gebracht.
* [Gratis proef versie](https://azure.microsoft.com/pricing/free-trial/): bevat tegoed voor gebruik met Microsoft Azure Services, maar biedt geen ondersteuning voor resources van derden. Als u services van derden of een betaalde MySQL-data base wilt kopen, moet u een abonnement voor een Credit Card gebruiken. U kunt voor Web Apps een gratis ClearDB MySQL-data base maken.
* [MSDN-abonnement](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) en **MSDN dev test betalen** naar gebruik: voor een MSDN-abonnement hebt u een credit card nodig om een betaalde mysql-oplossing van ClearDB te kunnen kopen, vergelijkbaar met een gratis proef versie.
* [Enterprise overeenkomst (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA-klanten worden per kwar taal gefactureerd op basis van hun EA voor al hun Azure Marketplace-aankopen (van derden) op een afzonderlijke, geconsolideerde factuur. U wordt buiten de Azure-voor uitbetaling (voorheen monetaire toezeg ging) gefactureerd voor Marketplace-aankopen. De Azure Store is op dit moment niet beschikbaar voor klanten die zijn Inge schreven in Azerbeidzjan, Kroatië, Noor wegen en Porto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Waarom werd $3,50 van de Azure Marketplace in rekening gebracht voor een web-app + MySQL?
De standaard optie voor de data base is Titan, een $3,50. De kosten worden niet weer gegeven tijdens het maken van de data base en u kunt per ongeluk een Data Base kopen die u niet wilt. We proberen de ervaring te verbeteren, maar tot nu toe moet u alle geselecteerde prijs categorieën voor web-app en data base controleren voordat u op **maken** klikt en de implementatie van de resources start.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Ik voer MySQL uit op mijn eigen virtuele machine van Azure. Kan ik mijn Azure-web-app verbinden met mijn data base?
Ja. U kunt uw web-app verbinden met uw data base zolang uw virtuele Azure-machine externe toegang heeft gekregen tot uw web-app. Zie [MySQL op een virtuele machine installeren](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor meer informatie.

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>In welke landen/regio's worden ClearDB Premium MySQL-clusters ondersteund?
ClearDB Premium MySQL-clusters zijn overal ter wereld beschikbaar in alle Azure-regio's, met uitzonde ring van India, Australië, Brazilië-zuid en China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Kan ik een nieuw cluster maken voorafgaand aan het maken van een Data Base met een ClearDB Premium-cluster oplossing?
Nee, het maken van lege ClearDB-clusters wordt niet ondersteund. Met de Azure Portal kunt u data bases in een cluster maken, waarmee u op hetzelfde moment een nieuw cluster kunt maken.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Wordt er een waarschuwing weer gegeven als ik een ClearDB MySQL-data base probeer te verwijderen die wordt gebruikt door een van mijn toepassingen?
Nee, in azure wordt u niet gewaarschuwd als u een Marketplace-aankoop verwijdert waarvan uw toepassing afhankelijk is.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>In welke regio's kan ik ClearDB-data bases maken?
Azure Marketplace is niet beschikbaar voor klanten die zijn Inge schreven in Azerbeidzjan, Kroatië, Noor wegen of Puerto Rico. ClearDB is niet beschikbaar in deze regio's.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Welke prijs categorie moet ik kiezen voor een web-app en Data Base voor productie?
Gebruik de basis-of een hogere prijs categorie voor Web Apps. Voor ClearDB kunt u het beste een Saturnus-of Jupiter-abonnement kiezen. Bekijk de functies & beperkingen van elke prijs categorie voor zowel [Web apps](https://azure.microsoft.com/pricing/details/app-service/) als [ClearDB MySQL-data bases](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) om het te kiezen dat aan uw behoeften voldoet.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hoe kan ik een upgrade van mijn ClearDB-Data Base naar een ander abonnement?
In de [Azure Portal](https://portal.azure.com)kunt u een gedeelde ClearDB-hosting database schalen. Lees dit [artikel](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) voor meer informatie. We bieden momenteel geen ondersteuning voor de upgrade voor ClearDB Premium-clusters in de Azure Portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Ik zie mijn ClearDB-data base niet in Azure Portal?
Als u een ClearDB-data base in het klassieke model hebt gemaakt, kunt u uw data base niet zien in de [Azure Portal](https://portal.azure.com). Er is geen tijdelijke oplossing voor dit scenario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Met wie moet ik contact opnemen voor ondersteuning wanneer mijn data base niet beschikbaar is?
Neem contact op met [ClearDB-ondersteuning](https://www.cleardb.com/developers/help/support) voor eventuele problemen met de data base. Bereid u voor om ze te voorzien van de informatie van uw Azure-abonnement.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Kan ik extra gebruikers maken voor mijn ClearDB MySQL-database cluster oplossing?
Nee. U kunt geen extra gebruikers maken, maar er kunnen wel aanvullende data bases worden gemaakt op uw ClearDB-database cluster.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Kunnen de Basic/Pro Series-data bases in-place worden bijgewerkt, vergelijkbaar met de plannen voor de ClearDB-Portal?
Ja, data bases van de Basic-serie kunnen worden bijgewerkt (Basic 60 tot en met Basic 500). Pro Series kan worden geüpgraded (Pro 125 t/m Pro 1000), met uitzonde ring van Pro 60. Upgrade Pro 60-data base wordt momenteel niet ondersteund. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Wanneer ik mijn resources van het ene naar het andere abonnement Migreer, wordt mijn ClearDB MySQL-data base ook gemigreerd?
Wanneer u een resource migratie uitvoert tussen abonnementen, zijn er enkele [beperkingen](azure-resource-manager/management/move-support-resources.md) van toepassing. Een ClearDB MySQL-data base is een service van derden en kan daarom niet worden gemigreerd tijdens de migratie van een Azure-abonnement. Als u de migratie van uw MySQL-data base niet beheert voordat u Azure-resources migreert, kunnen uw ClearDB MySQL-data bases worden uitgeschakeld. Migreer uw data bases eerst hand matig en voer vervolgens de migratie van het Azure-abonnement uit voor uw web-app. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Ik heb de bestedings limiet voor mijn abonnement bereikt. Ik heb de limiet verwijderd en mijn App Service is online, maar de data base is niet toegankelijk. Hoe kan ik de ClearDB-data base opnieuw inschakelen?
Neem contact op met de [ClearDB-ondersteuning](https://www.cleardb.com/developers/help/support) om de data base opnieuw in te scha kelen. Geef de gegevens van uw Azure-abonnement en de database naam op.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Kan ik een ClearDB-data base overdragen van een creditcard abonnement naar een EA-abonnement?
Bestaande ClearDB-data bases gebruiken de credit card die is gekoppeld aan de bestaande abonnementen. Als u een EA-abonnement wilt gebruiken, moet u uw gegevens naar een nieuwe data base migreren:

* Schaf een nieuwe ClearDB-data base aan bij uw EA-abonnement.
* Migreer uw gegevens naar uw nieuwe data base.
* Werk uw toepassing bij voor het gebruik van de nieuwe data base.
* Verwijder de oude ClearDB-data base.

Wanneer u een nieuwe web-app met MySQL (ClearDB) maakt of een MySQL-data base (ClearDB) maakt, bepaalt het abonnement dat u kiest hoe u betaalt voor de service. Met een EA-abonnement wordt de aanschaf van de services van derden, zoals ClearDB in de Azure Portal, niet geblokkeerd. EA-abonnementen worden buiten Azure-betaling gefactureerd en worden per kwar taal en achterstallig gefactureerd. De EA-klant zou een betaal wijze moeten instellen, zoals een credit card, om te betalen voor Marketplace-services van derden.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Waar kan ik de kosten voor ClearDB-resources in een EA-abonnement zien?
Voor direct EA-klanten zijn de kosten voor Azure Marketplace zichtbaar op het Enterprise Portal. Houd er rekening mee dat alle Marketplace-aankopen en-verbruik buiten Azure-betaling worden gefactureerd en worden per kwar taal en achterstallig worden gefactureerd. EA-klanten moeten rechtstreeks betalen aan de service providers van derden en kunnen dit doen door een betaal wijze zoals een credit card met hun EA-account in te scha kelen.

Indirecte EA-klanten kunnen hun Azure Marketplace-abonnementen vinden op de pagina **Abonnementen beheren** van de Enterprise Portal, maar de prijs is verborgen. Klanten kunnen contact opnemen met hun LSP voor informatie over de kosten voor Marketplace.

Toegang tot Azure Marketplace voor services van derden kan worden beheerd door uw EA Azure-inschrijvings beheerders. Ze kunnen de toegang tot aankopen van derden via de Store uitschakelen of opnieuw inschakelen in accounts en abonnementen beheren in de Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Met wie neem ik contact op met vragen over mijn factuur voor ClearDB Services in mijn EA-abonnement?
Neem contact op met de [klanten ondersteuning](https://aka.ms/AzureEntSupport) van het bedrijf met betrekking tot facturering onder hun EA-inschrijving. Het ondersteunings team van de EA-Portal beantwoordt uw vraag of helpt u bij het oplossen van uw probleem.

## <a name="more-information"></a>Meer informatie
[Veelgestelde vragen over Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)