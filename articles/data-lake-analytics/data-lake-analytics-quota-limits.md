---
title: Quota en limieten aanpassen in Azure Data Lake Analytics
description: Meer informatie over het aanpassen en verg Roten van quota's en limieten in Azure Data Lake Analytics-accounts (ADLA).
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710172"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Quota en limieten aanpassen in Azure Data Lake Analytics

Meer informatie over het aanpassen en verhogen van het quotum en de limieten in Azure Data Lake Analytics-accounts (ADLA). Als u deze limieten weet, kunt u beter inzicht krijgen in uw U-SQL-taak gedrag. Alle quota limieten zijn zacht, dus u kunt de maximum limiet verhogen door contact op te nemen met de ondersteuning van Azure.

## <a name="azure-subscriptions-limits"></a>Limieten voor Azure-abonnementen

**Maximum aantal ADLA-accounts per abonnement per regio:**  5

Als u een zesde ADLA-account maakt, wordt er een fout melding weer geven dat het maximum aantal toegestane Data Lake Analytics accounts (5) in de regio onder de naam van het abonnement is bereikt.

Als u de limiet wilt overschrijden, kunt u de volgende opties proberen:

- Kies een andere regio indien geschikt
- Neem contact op met de ondersteuning van Azure door [een ondersteunings ticket te openen](#increase-maximum-quota-limits) om een quotum toename aan te vragen.

## <a name="default-adla-account-limits"></a>Standaard limieten voor ADLA-accounts

**Maximum aantal Analytics units (AUs) per account:** 250, standaard 32

Dit is het maximum aantal van AUs dat gelijktijdig kan worden uitgevoerd in uw account. Als het totale aantal uitgevoerde AUs voor alle taken deze limiet overschrijdt, worden nieuwere taken in de wachtrij geplaatst. Bijvoorbeeld:

- Als er slechts één taak wordt uitgevoerd met 32 AUs, wanneer u een tweede taak indient, wacht deze in de taak wachtrij totdat de eerste taak is voltooid.
- Als er al vier taken worden uitgevoerd, wordt er 8 AUs gebruikt, wanneer u een vijfde taak indient die 8 AUs vereist, waarna de taak wachtrij 8 AUs beschikbaar heeft.

    ![Azure Data Lake Analytics limieten en quotum pagina](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximum aantal Analytics units (AUs) per taak:** 250, standaard 32

Dit is het maximum aantal van AUs dat elke afzonderlijke taak kan worden toegewezen in uw account. Taken die meer dan deze limiet worden toegewezen, worden geweigerd, tenzij de indiener wordt beïnvloed door een berekenings beleid (taak verzendings limiet) waarmee ze meer AUs per taak bieden. De bovengrens van deze waarde is de AU-limiet voor het account.

**Maximum aantal gelijktijdige U-SQL-taken per account:** 20

Dit is het maximum aantal taken dat gelijktijdig kan worden uitgevoerd in uw account. Boven deze waarde worden nieuwere taken automatisch in de wachtrij geplaatst.

## <a name="adjust-adla-account-limits"></a>Limieten voor ADLA-accounts aanpassen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Kies een bestaand ADLA-account.
3. Klik op **Eigenschappen**.
4. U kunt de waarden voor het **maximum aantal AUs**, het **maximum van de actieve taken** en de **Verzend limieten** van taken aanpassen aan uw behoeften.

## <a name="increase-maximum-quota-limits"></a>Maximum aantal quotum limieten verhogen

Meer informatie over Azure-limieten vindt u in de documentatie voor de [specifieke limieten van Azure-service](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Open een ondersteunings aanvraag in Azure Portal.

   ![Azure Data Lake Analytics portal: Help en ondersteuning](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Nieuwe ondersteunings aanvraag voor Azure Data Lake Analytics Portal](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Selecteer het type **quotum** van het probleem.

3. Selecteer uw **abonnement** (zorg ervoor dat het geen ' proef abonnement ' is).

4. Selecteer quotum type **Data Lake Analytics**.

   ![Type quotum voor ondersteunings aanvraag Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Op de pagina probleem moet u de aangevraagde verhogings limiet uitleggen met **Details** over de reden waarom u deze extra capaciteit nodig hebt.

   ![Details van de ondersteunings aanvraag Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Controleer uw contact gegevens en maak de ondersteunings aanvraag.

Micro soft bekijkt uw aanvraag en probeert zo snel mogelijk uw bedrijfs behoeften aan te passen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
