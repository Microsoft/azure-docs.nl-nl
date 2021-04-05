---
title: Beleid voor Azure Data Lake Analytics-accounts beheren
description: Meer informatie over het gebruik van account beleid voor het beheren van het gebruik van een Data Lake Analytics account, zoals maximum aantal AUs en maximum aantal taken.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013306"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Azure Data Lake Analytics beheren met account beleid

Met account beleid kunt u bepalen hoe resources een Azure Data Lake Analytics-account gebruikt. Met deze beleids regels kunt u de kosten van het gebruik van Azure Data Lake Analytics beheren. Met deze beleids regels kunt u bijvoorbeeld onverwachte kosten pieken voor komen door te beperken hoeveel AUs het account gelijktijdig kan gebruiken. # # beleids regels op account niveau

Dit beleid is van toepassing op alle taken in een Data Lake Analytics-account.

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximum aantal AUs in een Data Lake Analytics-account

Een beleid bepaalt het totale aantal Analytics units (AUs) dat uw Data Lake Analytics-account kan gebruiken. De waarde is standaard ingesteld op 250. Als deze waarde bijvoorbeeld is ingesteld op 250 AUs, kunt u een taak die wordt uitgevoerd met 250 AUs hieraan toewijzen, of 10 taken die worden uitgevoerd met 25 AUs elk. Aanvullende taken die worden verzonden, worden in een wachtrij geplaatst totdat de actieve taken zijn voltooid. Wanneer het uitvoeren van taken is voltooid, worden AUs vrijgegeven voor het uitvoeren van de taken in de wachtrij.

Het aantal AUs voor uw Data Lake Analytics account wijzigen:

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **limieten en beleids regels**.
3. Verplaats onder **Maxi maal AUs** de schuif regelaar om een waarde te selecteren of voer de waarde in het tekstvak in.
4. Klik op **Opslaan**.

   > [!NOTE]
   > Als u meer nodig hebt dan de standaard (250) AUs, klikt u in de portal op **Help + ondersteuning** om een ondersteunings aanvraag in te dienen. Het aantal in de Data Lake Analytics-account beschik bare AUs kan worden verhoogd.

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximum aantal taken dat tegelijkertijd kan worden uitgevoerd

Dit beleid beperkt het aantal taken dat tegelijkertijd kan worden uitgevoerd. Deze waarde is standaard ingesteld op 20. Als uw Data Lake Analytics AUs beschikbaar heeft, worden nieuwe taken zo gepland dat deze onmiddellijk worden uitgevoerd tot het totale aantal actieve taken de waarde van dit beleid bereikt. Wanneer u het maximum aantal taken bereikt dat tegelijkertijd kan worden uitgevoerd, worden volgende taken in de volg orde van prioriteit in de wachtrij geplaatst totdat een of meer actieve taken zijn voltooid (afhankelijk van de beschik bare AUs).

Wijzigen van het aantal taken dat tegelijkertijd kan worden uitgevoerd:

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **limieten en beleids regels**.
3. Verplaats onder **maximum aantal actieve taken** de schuif regelaar om een waarde te selecteren of voer de waarde in het tekstvak in.
4. Klik op **Opslaan**.

   > [!NOTE]
   > Als u meer dan het standaard aantal taken (20) wilt uitvoeren, klikt u in de portal op **Help en ondersteuning** om een ondersteunings aanvraag in te dienen. Het aantal taken dat tegelijkertijd kan worden uitgevoerd in uw Data Lake Analytics-account kan worden verhoogd.

## <a name="how-long-to-keep-job-metadata-and-resources"></a>Hoe lang de meta gegevens en resources van de taak bewaard moeten blijven

Wanneer uw gebruikers U-SQL-taken uitvoeren, worden alle gerelateerde bestanden door de Data Lake Analytics-service bewaard. Deze bestanden bevatten het U-SQL-script, de DLL-bestanden waarnaar wordt verwezen in het U-SQL-script, gecompileerde resources en statistieken. De bestanden bevinden zich in de map/System/van het standaard Azure Data Lake Storage-account. Dit beleid bepaalt hoe lang deze resources worden opgeslagen voordat ze automatisch worden verwijderd (de standaard waarde is 30 dagen). U kunt deze bestanden gebruiken voor fout opsporing en voor het afstemmen van de prestaties van taken die u in de toekomst opnieuw wilt uitvoeren.

Instellen hoe lang de meta gegevens en resources van de taak moeten worden bewaard:

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **limieten en beleids regels**.
3. Verplaats onder **dagen om taak query's te bewaren** de schuif regelaar om een waarde te selecteren of voer de waarde in het tekstvak in.  
4. Klik op **Opslaan**.

## <a name="job-level-policies"></a>Beleids regels op taak niveau

Met beleids regels op taak niveau kunt u het maximum aantal AUs en de maximale prioriteit instellen waarmee afzonderlijke gebruikers (of leden van specifieke beveiligings groepen) kunnen worden ingesteld voor taken die ze indienen. Met dit beleid kunt u de kosten beheren die door gebruikers worden gemaakt. U kunt hiermee ook het effect bepalen dat geplande taken mogelijk hebben op productie taken met hoge prioriteit die worden uitgevoerd in hetzelfde Data Lake Analytics-account.

Data Lake Analytics heeft twee beleids regels die u op taak niveau kunt instellen:

- **Automatische au-limiet per taak**: gebruikers kunnen alleen taken verzenden die Maxi maal het aantal AUs hebben. Deze limiet is standaard hetzelfde als de maximale AU-limiet voor het account.

- **Prioriteit**: gebruikers kunnen alleen taken verzenden die een prioriteit hebben die lager is dan of gelijk is aan deze waarde. Een hogere waarde geeft aan dat er een lagere prioriteit is. Deze limiet is standaard ingesteld op 1. Dit is de hoogst mogelijke prioriteit.

Er is een standaard beleid ingesteld voor elk account. Het standaard beleid is van toepassing op alle gebruikers van het account. U kunt extra beleids regels maken voor specifieke gebruikers en groepen.

> [!NOTE]
> Beleids regels op account niveau en beleids regels op taak niveau worden tegelijkertijd toegepast.

## <a name="add-a-policy-for-a-specific-user-or-group"></a>Een beleid voor een specifieke gebruiker of groep toevoegen

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.

2. Klik op **limieten en beleids regels**.

3. Klik onder **limieten voor taak indiening** op de knop **beleid toevoegen** . Selecteer of voer de volgende instellingen in:

   1. **Naam van reken beleid**: Voer een beleids naam in om u te herinneren over het doel van het beleid.

   2. **Gebruiker of groep selecteren**: Selecteer de gebruiker of groep waarop dit beleid van toepassing is.

   3. **De limiet voor taak-au instellen**: Stel de limiet voor au in die van toepassing is op de geselecteerde gebruiker of groep.

   4. **Stel de prioriteits limiet** in: Stel de prioriteits limiet in die van toepassing is op de geselecteerde gebruiker of groep.

4. Klik op **OK**.

5. Het nieuwe beleid wordt weer gegeven in de **standaard** beleids tabel, onder **limieten voor taak inzending**.

## <a name="delete-or-edit-an-existing-policy"></a>Bestaande beleids regels verwijderen of bewerken

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.

2. Klik op **limieten en beleids regels**.

3. Zoek onder **limieten voor taak inzending** het beleid dat u wilt bewerken.

4. Als u de opties voor **verwijderen** en **bewerken** wilt zien, klikt u in de kolom uiterst rechts van de tabel op `...` . # # extra resources voor taak beleid

- [Blog bericht over beleids overzicht](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [Blog bericht beleids regels op account niveau](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [Blog bericht over beleid op taak niveau](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
- [Aan de slag met Data Lake Analytics met behulp van de Azure Portal](data-lake-analytics-get-started-portal.md)
- [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)