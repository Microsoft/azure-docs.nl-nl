---
title: Azure-gebruik ophalen met Azure Billing-API's
description: Informatie over Azure Billing Usage en RateCard-API's, die worden gebruikt om inzicht te krijgen in het gebruik en trends van Azure-resources.
author: tonguyen
ms.reviewer: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 15e8a31c7ca3b87a8bf92a520bcf07de1e063efd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599144"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Gebruik Azure Billing-API's voor het op programmatische wijze verkrijgen van inzicht in uw gebruik van Azure
Gebruik Azure Billing-API's voor het opnemen van gebruiks- en resourcegegevens in uw favoriete hulpprogramma's voor gegevensanalyse. De Azure Resource Usage- en RateCard-API’s kunnen u helpen uw kosten nauwkeurig te voorspellen en te beheren. De API's worden geïmplementeerd als resourceprovider en maken deel uit van de API-familie die beschikbaar wordt gesteld door Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API Azure-factuur downloaden (preview)
Nadat het [aanmelden is voltooid](manage-billing-access.md#opt-in), kunt u facturen downloaden met behulp van de preview-versie van de [Factuur-API](/rest/api/billing). De functies zijn onder andere:

* **Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)** : configureer toegangsbeleid in [Azure Portal](https://portal.azure.com) of via [Azure PowerShell-cmdlets](/powershell/azure/) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Facturering voor lezer, Lezer, Eigenaar of Bijdrager om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
* **Datumfilters**: gebruik de parameter `$filter` om alle facturen in omgekeerde chronologische volgorde op te halen op basis van de einddatum van de factuurperiode.

> [!NOTE]
> Deze functie bevindt zich in de eerste preview-versie. Mogelijk worden er wijzigingen doorgevoerd die niet achterwaarts compatibel zijn. De functie is momenteel niet beschikbaar voor bepaalde abonnementstypen (EA, CSP en AIO worden niet ondersteund) en voor Azure Duitsland.

## <a name="azure-resource-usage-api-preview"></a>Azure-resourcegebruik-API (preview)
Gebruik de [Azure-resourcegebruik-API](/previous-versions/azure/reference/mt219003(v=azure.100)) om geschatte gegevens over het Azure-gebruik op te halen. De API omvat:

* **Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)** : configureer toegangsbeleid in [Azure Portal](https://portal.azure.com) of via [Azure PowerShell-cmdlets](/powershell/azure/) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de gebruiksgegevens van het abonnement. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer van facturering, Lezer, Eigenaar of Inzender om toegang te krijgen tot de gebruiksgegevens voor een specifiek Azure-abonnement.
* **Aggregaties per uur of per dag**: aanroepers kunnen opgeven of ze hun Azure-gebruiksgegevens in buckets per uur of per dag willen. De standaardinstelling is per dag.
* **Metagegevens van exemplaren (inclusief resourcetags**): haal details op exemplaarniveau op, zoals de volledig gekwalificeerde resource-URI (/subscriptions/{subscription-id}/..), informatie over de resourcegroep en resourcetags. Deze metagegevens helpen u op een deterministische en programmatische manier gebruik toe te wijzen aan de tags, bijvoorbeeld voor kostendoorrekeningen.
* **Metagegevens van resources**: resourcegegevens zoals de naam, categorie en subcategorie van de meter, eenheid en regio geven de aanroeper een beter inzicht in wat er is verbruikt. We werken ook aan het gelijktrekken van de terminologie voor resourcemetagegevens in de Azure-portal, CSV voor Azure-gebruik, EA Billing CSV en andere openbare ervaringen, zodat u gegevens in verschillende ervaringen kunt correleren.
* **Gebruik voor verschillende aanbiedingstypen**: gebruiksgegevens zijn beschikbaar voor aanbiedingstypen zoals betalen per gebruik, MSDN, Azure-vooruitbetaling (voorheen financiële toezegging), Azure-vooruitbetalingstegoed en EA, met uitzondering van [CSP](/partner-center).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-resource-RateCard-API (preview)
Gebruik de[Azure Resource RateCard-API](/previous-versions/azure/reference/mt219005(v=azure.100)) om een lijst met beschikbare Azure-resources op te halen, evenals geschatte prijsinformatie voor elke resource. De API omvat:

* **Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)** : configureer uw toegangsbeleid in [Azure Portal](https://portal.azure.com) of via [Azure PowerShell-cmdlets](/powershell/azure/) om op te geven welke gebruikers of toepassingen toegang kunnen krijgen tot de RateCard-gegevens. Aanroepers moeten standaard Azure Active Directory-tokens gebruiken voor verificatie. Voeg de aanroeper toe aan de rol Lezer, Eigenaar of Bijdrager om toegang te krijgen tot de gebruiksgegevens voor een bepaald Azure-abonnement.
* **Ondersteuning voor de aanbiedingstypen betalen per gebruik, MSDN, Azure-vooruitbetalingstegoed (EA en [CSP](/partner-center) worden niet ondersteund)** : deze API biedt informatie over de tarieven voor Azure.  De aanroeper van deze API moet de aanbiedingsgegevens invoeren om de resourcedetails en-tarieven op te halen. We kunnen momenteel geen EA-tarieven verstrekken omdat EA-aanbiedingen aangepaste tarieven per inschrijving hebben.

## <a name="scenarios"></a>Scenario's
Hier volgen enkele van de scenario's die mogelijk worden gemaakt met de combinatie van de gebruiks- en de RateCard-API:

* **Azure-uitgaven gedurende de maand**: gebruik de combinatie van de API's voor gebruik en RateCard om in de loop van de maand beter inzicht te krijgen in uw clouduitgaven. U kunt het gebruik en de kostenramingen per uur en per dag analyseren.
* **Waarschuwingen instellen**: gebruik de API's voor gebruik en RateCard om het geschatte cloudverbruik en de bijbehorende kosten te verkrijgen en stel waarschuwingen in op basis van resources of geld.
* **Factuurvoorspelling**: ontvang het geschatte verbruik en de clouduitgaven en pas machine learning-algoritmen toe om de hoogte van de factuur aan het einde van de factureringscyclus te voorspellen.
* **Kostenanalyse vóór verbruik**: gebruik de RateCard-API om te voorspellen hoe hoog uw factuur voor uw verwachte gebruik zou zijn wanneer u uw workloads naar Azure verplaatst. Als u bestaande workloads in andere clouds of particuliere clouds hebt, kunt u uw gebruik ook toewijzen aan de Azure-tarieven om een betere schatting te krijgen van de uitgaven met Azure. Met deze schatting kunt u gebruikmaken van aanbiedingen en de verschillende typen aanbiedingen vergelijken naast betalen per gebruik, zoals Azure-vooruitbetaling en Azure-vooruitbetalingstegoed. Met de API kunt u ook de kostenverschillen per regio bekijken en een wat-als-kostenanalyse uitvoeren om u te helpen bij het nemen van de juiste beslissingen voor een implementatie.
* **Wat-als-analyse** -

  * U kunt bepalen of het rendabeler is om workloads uit te voeren in een andere regio of met een andere configuratie van de Azure-resource. Kosten voor Azure-resources kunnen verschillen afhankelijk van de Azure-regio die u gebruikt.
  * U kunt ook bepalen of een ander Azure-aanbiedingstype een beter tarief voor een Azure-resource biedt.


## <a name="next-steps"></a>Volgende stappen
* Bekijk de codevoorbeelden op GitHub:
  * [Codevoorbeeld Invoice API](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Codevoorbeeld Usage API](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Codevoorbeeld RateCard API](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Zie [Overzicht Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie over Azure Resource Manager.