---
title: Inzicht in het werken met Azure Cost Management-gegevens
description: Dit artikel helpt u om een beter beeld te krijgen van gegevens die worden opgenomen in Azure Cost Management en hoe vaak deze worden verwerkt, verzameld, weergegeven en afgesloten.
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: 97ae2ba26818bbc306da71af814d9b4f95858b6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032572"
---
# <a name="understand-cost-management-data"></a>Inzicht in gegevens van Cost Management

Dit artikel helpt u beter inzicht te krijgen in de kosten en gebruiksgegevens van Azure die worden opgenomen in Azure Cost Management. Er wordt uitgelegd hoe vaak gegevens worden verwerkt, verzameld, weergegeven en afgesloten. U wordt maandelijks gefactureerd voor Azure-gebruik. Hoewel factureringscycli maandelijkse perioden zijn, verschillen de begin- en einddatums van een cyclus per abonnementstype. Hoe vaak Cost Management gebruiksgegevens ontvangt, is afhankelijk van verschillende factoren. Hoe lang het duurt om de gegevens te verwerken en hoe vaak Azure-services gebruik doorgeven aan het factureringssysteem zijn voorbeelden van dergelijke factoren.

Cost Management omvat alle gebruik en aankopen, inclusief reserveringen en aanbiedingen van derden voor EA-accounts (Enterprise Agreement). Accounts van een Microsoft-gebruikersovereenkomst en individuele abonnementen met betalen per gebruik-tarieven omvatten alleen verbruik van Azure- en Marketplace-services. Ondersteuning en andere kosten zijn niet inbegrepen. Kosten worden geschat totdat er een factuur wordt gegenereerd en er wordt geen rekening gehouden met tegoeden.

Als u een nieuw abonnement hebt, kunt u de Cost Management-functies niet meteen gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

## <a name="supported-microsoft-azure-offers"></a>Ondersteunde Microsoft Azure-aanbiedingen

In de onderstaande tabel vindt u informatie over de momenteel ondersteunde [Microsoft Azure-aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/) in Azure Cost Management. Een Azure-aanbieding is het type Azure-abonnement dat u hebt. In Cost Management zijn gegevens beschikbaar vanaf de datum bij **Gegevens beschikbaar vanaf**. Als de aanbieding voor een abonnement verandert, zijn de kosten vóór de wijzigingsdatum van de aanbieding niet beschikbaar.

| **Categorie**  | **Naam van aanbieding** | **Quotum-id** | **Nummer van aanbieding** | **Gegevens beschikbaar vanaf** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | mei 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | mei 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | mei 2014<sup>1</sup> |
| **Microsoft-klantovereenkomst** | Microsoft Azure Plan | EnterpriseAgreement_2014-09-01 | N.v.t. | maart 2019<sup>3</sup> |
| **Microsoft-klantovereenkomst** | Microsoft Azure Plan voor Dev/Test | MSDNDevTest_2014-09-01 | N.v.t. | maart 2019<sup>3</sup> |
| **Microsoft-klantovereenkomst ondersteund door partners** | Microsoft Azure Plan | CSP_2015-05-01, CSP_MG_2017-12-01 en CSPDEVTEST_2018-05-01<br><br>De quotum-id wordt opnieuw gebruikt voor abonnementen in het kader van een Microsoft-klantovereenkomst en verouderde CSP-abonnementen. Op dit moment worden alleen abonnementen van een Microsoft-klantovereenkomst ondersteund. | N.v.t. | Oktober 2019 |
| **Microsoft Developer Network (MSDN)** | MSDN Platforms<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Betalen per gebruik                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Dev/Test met betalen per gebruik         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Gratis proefversie<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Azure in Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 oktober 2018<sup>2</sup> |

_<sup>**1**</sup> Voor gegevens van vóór 2014 mei gaat u naar de [Azure Enterprise-portal](https://ea.azure.com)._

_<sup>**2**</sup> Voor gegevens van vóór 2 oktober 2018, gaat u naar het [Azure-accountcentrum](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft-klantovereenkomsten die zijn gestart in maart 2019 en die geen historische gegevens hebben vóór dit punt._

_<sup>**4**</sup> Historische gegevens voor abonnementen die zijn gebaseerd op tegoeden en vooraf betalen, komen mogelijk niet overeen met uw factuur. Zie [Historische gegevens komen mogelijk niet overeenkomen met de factuur](#historical-data-might-not-match-invoice) hieronder._

De volgende aanbiedingen worden nog niet ondersteund:

| Categorie  | **Naam van aanbieding** | **Quotum-id** | **Nummer van aanbieding** |
| --- | --- | --- | --- |
| **Azure Duitsland** | Azure Duitsland - Betalen per gebruik | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government - Betalen per gebruik | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Duitsland in CSP voor Microsoft Cloud Duitsland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Betalen per gebruik**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Betalen per gebruik** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Betalen per gebruik**                 | Microsoft Azure Sponsorship | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Ondersteuningsplannen** | Standard-ondersteuning                    | Default_2014-09-01 | MS-AZR-0041P |
| **Ondersteuningsplannen** | Professional Direct-ondersteuning         | Default_2014-09-01 | MS-AZR-0042P |
| **Ondersteuningsplannen** | Developer-ondersteuning                   | Default_2014-09-01 | MS-AZR-0043P |
| **Ondersteuningsplannen** | Ondersteuningsplan Duitsland                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Ondersteuningsplannen** | Standard-ondersteuning Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Ondersteuningsplannen** | Pro Direct-ondersteuning Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Ondersteuningsplannen** | Developer-ondersteuning Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Upgrade van gratis proefversie naar betalen per gebruik

Raadpleeg de [Veelgestelde vragen over een gratis Azure-account](https://azure.microsoft.com/free/free-account-faq/) voor informatie over de beschikbaarheid van services in de gratis laag, nadat u een upgrade hebt uitgevoerd van de gratis proefversie naar betalen per gebruik.

### <a name="determine-your-offer-type"></a>Uw type aanbieding bepalen

Als u geen gegevens voor een abonnement ziet en u wilt bepalen of uw abonnement onder de ondersteunde aanbiedingen valt, kunt u controleren of uw abonnement wordt ondersteund. Als u wilt valideren of een Azure-abonnement wordt ondersteund, meldt u zich aan bij de Azure-portal. Selecteer vervolgens **Alle services** in het linkerdeelvenster. Selecteer **Abonnementen** in de lijst met services. Selecteer in het menu van de lijst met abonnementen het abonnement dat u wilt controleren. Uw abonnement wordt weergegeven op het tabblad Overzicht en u ziet daar onder andere de **aanbieding** en de **aanbieding-id**. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeld van het tabblad Overzicht van een abonnement met de naam en id van de aanbieding](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kosten opgenomen in Cost Management

In de volgende tabellen ziet u welke gegevens wel en niet worden opgenomen in Cost Management. Alle kosten worden geschat totdat er een factuur wordt gegenereerd. Gratis en vooruitbetaalde tegoeden zijn niet inbegrepen in de kosten.

| **Inbegrepen** | **Niet inbegrepen** |
| --- | --- |
| Gebruik van Azure-service<sup>5</sup>        | Ondersteuningskosten: zie [De voorwaarden op uw Microsoft Azure-factuur begrijpen](../understand/understand-invoice.md) voor meer informatie. |
| Gebruik van Marketplace-aanbieding<sup>6</sup> | Belastingen: zie [De voorwaarden op uw Microsoft Azure-factuur begrijpen](../understand/understand-invoice.md) voor meer informatie. |
| Aankopen op Marketplace<sup>6</sup>      | Tegoeden: zie [De voorwaarden op uw Microsoft Azure-factuur begrijpen](../understand/understand-invoice.md) voor meer informatie. |
| Reserveringsaankopen<sup>7</sup>      |  |
| Afschrijving van reserveringsaankopen<sup>7</sup>      |  |

_<sup>**5**</sup> Gebruik van Azure-service is gebaseerd op reservering en overeengekomen prijzen._

_<sup>**6**</sup> Marketplace-aankopen zijn op dit moment niet beschikbaar voor aanbiedingen van MSDN en Visual Studio._

_<sup>**7**</sup> Reserveringsaankopen zijn op dit moment alleen beschikbaar voor EA-accounts (Enterprise Agreement) en Microsoft-klantovereenkomstaccounts._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Hoe tags worden gebruikt bij kosten- en gebruiksgegevens

Azure Cost Management ontvangt tags als onderdeel van elke gebruiksrecord die door de afzonderlijke services wordt verzonden. De volgende beperkingen zijn van toepassing op deze tags:

- Tags moeten rechtstreeks worden toegepast op resources en worden niet impliciet overgenomen van de bovenliggende resourcegroep.
- Resourcetags worden alleen ondersteund voor resources die zijn geïmplementeerd voor resourcegroepen.
- Sommige geïmplementeerde resources bieden mogelijk geen ondersteuning voor tags of bevatten geen tags in gebruiksgegevens.
- Resourcetags worden alleen opgenomen in gebruiksgegevens terwijl de tag wordt toegepast: tags worden niet toegepast op historische gegevens.
- Resourcetags zijn alleen beschikbaar in Cost Management nadat de gegevens zijn vernieuwd.
- Resourcetags zijn alleen beschikbaar in Cost Management wanneer de resource actief is of wordt uitgevoerd en gebruiksrecords produceert (dus niet wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt).
- Voor het beheren van tags moet de inzender toegang tot elke resource hebben.
- Voor het beheren van tagbeleid moet de eigenaar of beleidsinzender toegang hebben tot een beheergroep, abonnement of resourcegroep.
    
Als een specifieke tag niet wordt weergegeven in Cost Management, ga dan het volgende na:

- Is de tag rechtstreeks toegepast op de resource?
- Is de tag meer dan 24 uur geleden toegepast?
- Ondersteunt het resourcetype tags? De volgende resourcetypen bieden geen ondersteuning voor tags in gebruiksgegevens vanaf 1 december 2019. Zie [Tagsondersteuning voor Azure-resources](../../azure-resource-manager/management/tag-support.md) voor een volledige lijst van wat er wordt ondersteund.
    - Azure Active Directory B2C
    - Azure Bastion
    - Azure-firewalls
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Load balancers
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Hier volgen enkele tips voor het werken met tags:

- Plan vooruit en definieer een coderingsstrategie waarmee u de kosten per organisatie, toepassing, omgeving, enzovoort, kunt opsplitsen.
- Gebruik Azure Policy om de tags van een resourcegroep te kopiëren naar afzonderlijke resources en uw coderingsstrategie af te dwingen.
- Gebruik de Tags-API in combinatie met Query of UsageDetails om alle kosten op basis van de huidige tags op te halen.


## <a name="cost-and-usage-data-updates-and-retention"></a>Updates en retentie voor kosten- en gebruiksgegevens

Kosten en gebruiksgegevens zijn gewoonlijk binnen 8 tot 24 uur beschikbaar in Cost Management en Facturering in de Azure-portal en in ondersteunende API's. Houd rekening met de volgende punten wanneer u kosten gaat bekijken:

- Voor elke Azure-service (zoals Azure Storage, Azure Compute en Azure SQL) worden gebruiksgegevens verzonden op verschillende tijden: gegevens voor sommige services worden eerder weergegeven dan andere.
- Geschatte kosten voor de huidige factureringsperiode worden zes keer per dag bijgewerkt.
- Geschatte kosten voor de huidige factureringsperiode kunnen veranderen wanneer u meer gebruikt.
- Elke update is cumulatief en bevat alle regelitems en gegevens van de vorige update.
- De huidige factureringsperiode wordt voltooid of _afgesloten_ door Azure als er maximaal 72 uur (drie kalenderdagen) zijn verstreken nadat de factureringsperiode is beëindigd.

In de volgende voorbeelden ziet u hoe factureringsperioden kunnen worden beëindigd:

* EA-abonnementen (Enterprise Agreement): als de factureringsmaand eindigt op 31 maart, worden de geschatte kosten maximaal 72 uur later bijgewerkt. In dit voorbeeld zou dat middernacht (UTC) zijn op 4 april.
* Abonnementen van betalen per gebruik: als de factureringsmaand eindigt op 15 mei, worden de geschatte kosten maximaal 72 uur later bijgewerkt. In dit voorbeeld zou dat middernacht (UTC) zijn op 19 mei.

Zodra de kosten- en gebruiksgegevens beschikbaar zijn in Cost Management en Facturering, wordt deze ten minste 7 jaar bewaard.

### <a name="rerated-data"></a>Opnieuw berekende tarieven

Ongeacht of u gegevens ophaalt met behulp van de API's van Cost Management, Power BI of de Azure-portal, moet u er rekening mee houden dat het tarief voor de kosten van de huidige factureringsperiode opnieuw wordt berekend totdat de factuur is gesloten en dat de kosten dus kunnen veranderen.

## <a name="cost-rounding"></a>Kostenafronding

De in Cost Management weergegeven kosten worden afgerond. Kosten die worden geretourneerd met de query-API, worden niet afgerond. Bijvoorbeeld:

- Kostenanalyse in de Azure-portal: kosten worden afgerond met behulp van de standaardregels voor afronding, dat wil zeggen dat waarden boven 0,5 naar boven worden afgerond, en dat in andere gevallen naar beneden wordt afgerond. Afronden gebeurt alleen wanneer waarden worden weergegeven. Er wordt niet afgerond tijdens het verwerken en samenvoegen van gegevens. Voor kostenanalyse worden kosten bijvoorbeeld als volgt samengevoegd:
  -    Kosten 1: $ 0,004
  - Kosten 2: $ 0,004
  -    Weergegeven samengevoegde kosten: 0,004 + 0,004 = 0,008. De weergegeven kosten zijn $ 0,01.
- Query-API: de kosten worden weergegeven met acht decimalen en worden niet afgerond.

## <a name="historical-data-might-not-match-invoice"></a>Historische gegevens komen mogelijk niet overeenkomen met de factuur

Historische gegevens voor aanbiedingen die zijn gebaseerd op tegoeden en vooraf betalen, komen mogelijk niet overeen met uw factuur. Bij sommige Azure-aanbiedingen van betalen per gebruik, MSDN en Visual Studio kunnen tegoeden en voorafbetalingen van Azure op de factuur worden toegepast. De historische gegevens die in Cost Management worden weergegeven, zijn echter alleen gebaseerd op uw geschatte verbruikskosten. Historische gegevens van Cost Management bevatten geen betalingen en tegoeden. Het is dus mogelijk dat historische gegevens die worden weergegeven voor de volgende aanbiedingen mogelijk niet exact overeen met uw factuur.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Gratis proefversie (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Zie ook

- Als u de eerste quickstart voor Cost Management nog niet hebt voltooid, leest u die op [Kosten analyseren](./quick-acm-cost-analysis.md).