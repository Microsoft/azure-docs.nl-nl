---
title: Problemen met de Azure Data Lake Analytics U-SQL runtime-fouten oplossen
description: Meer informatie over het oplossen van fouten in U-SQL-runtime.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030610"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Meer informatie over het oplossen van problemen met U-SQL-runtime als gevolg van runtime wijzigingen

De Azure Data Lake U-SQL-runtime, met inbegrip van compiler, optimizer en taakbeheer, is datgene waarmee uw U-SQL-code wordt verwerkt.

## <a name="choosing-your-u-sql-runtime-version"></a>Uw U-SQL-runtime versie kiezen

Wanneer u u-SQL-taken vanuit Visual Studio, de ADL-SDK of de Azure Data Lake Analytics Portal verzendt, gebruikt uw taak de momenteel beschik bare standaard runtime. Nieuwe versies van de U-SQL-runtime worden regel matig vrijgegeven en bevatten kleine updates en beveiligings oplossingen.

U kunt ook een aangepaste runtime versie kiezen. omdat u een nieuwe update wilt uitproberen, moet u een oudere versie van een runtime blijven of zijn voorzien van een hotfix voor een gemeld probleem waarin u niet kunt wachten op de gewone nieuwe update.

> [!CAUTION]
> Als u een runtime kiest die verschilt van de standaardinstelling, kunt u uw U-SQL-taken onderbreken. Gebruik deze andere versies alleen voor test doeleinden.

In zeldzame gevallen kan Microsoft Ondersteuning een andere versie van een runtime vastmaken als de standaard waarde voor uw account. Zorg ervoor dat u deze pincode zo snel mogelijk herstelt. Als u vastmaakt aan die versie, verloopt deze op een later tijdstip.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>De runtime-versie van uw taken controleren

U kunt de geschiedenis bekijken van welke runtime versie uw vorige taken hebben gebruikt in de taak geschiedenis van uw account via de job browser van Visual Studio of de taak geschiedenis van de Azure Portal.

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Selecteer **alle taken weer geven**. Er wordt een lijst weer gegeven met alle actieve en recent voltooide taken in het account.
3. Klik desgewenst op **filter** om u te helpen de taken te vinden op **tijds bereik**, **taak naam** en waarden voor **Auteur** .
4. U kunt de runtime zien die wordt gebruikt in de voltooide taken.

![De runtime versie van een eerdere taak weer geven](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

De beschik bare runtime versies veranderen in de loop van de tijd. De standaard runtime wordt altijd ' default ' genoemd en we blijven ten minste de vorige runtime voor enige tijd beschikbaar, en u kunt speciale Runtimes om verschillende redenen beschikbaar maken. Expliciet benoemde Runtimes volgen doorgaans de volgende indeling (cursief worden gebruikt voor variabele onderdelen en [] duidt optionele onderdelen aan):

release_YYYYMMDD_adl_buildno [_modifier]

Release_20190318_adl_3394512_2 betekent bijvoorbeeld dat de tweede versie van de build 3394512 van de runtime-versie van maart 18 2019 en release_20190318_adl_3394512_private een persoonlijke build van dezelfde release. Opmerking: de datum is gerelateerd aan wanneer de laatste controle is uitgevoerd voor die versie en niet noodzakelijkerwijs de officiële release datum.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemen met de versie van SQL runtime oplossen

Er zijn twee mogelijke runtime-versie problemen die kunnen optreden:

1. Een script of gebruikers code verandert het gedrag van een release in de volgende. Dergelijke breuk wijzigingen worden normaal gesp roken vooraf door gegeven aan de publicatie van release opmerkingen. Als u een dergelijke wijziging tegen komt, neemt u contact op met Microsoft Ondersteuning om dit Afbrekings gedrag te melden (als dit nog niet is gedocumenteerd) en verzendt u uw taken voor de oudere runtime versie.

2. U hebt een niet-standaard runtime gebruikt, hetzij expliciet, impliciet als deze is vastgemaakt aan uw account en deze runtime is na enige tijd verwijderd. Als er ontbrekende Runtimes optreden, moet u uw scripts bijwerken om uit te voeren met de huidige standaard runtime. Als u meer tijd nodig hebt, neemt u contact op met Microsoft Ondersteuning

## <a name="known-issues"></a>Bekende problemen

1. Als u verwijst naar Newtonsoft.Jsin File Version 12.0.3 of gaat u in een USQL-script, treedt de volgende compilatie fout op:

    *"We vinden het jammer. taken die worden uitgevoerd in uw Data Lake Analytics-account, zullen waarschijnlijk langzamer worden uitgevoerd of niet worden voltooid. Deze functionaliteit kan niet automatisch worden hersteld naar uw Azure Data Lake Analytics-account vanwege een onverwacht probleem. Er is contact gemaakt met Azure Data Lake engineers om te onderzoeken. "*  

    Waarbij de aanroep stack het volgende bevat:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Oplossing**: gebruik Newtonsoft.Jsop File v 12.0.2 of lager.
2. Klanten zien mogelijk tijdelijke bestanden en mappen in de Store. Deze worden geproduceerd als onderdeel van de normale taak uitvoering, maar worden doorgaans verwijderd voordat de klanten ze zien. Onder bepaalde omstandigheden, die zeldzame en wille keurig zijn, kunnen ze gedurende een bepaalde tijd zichtbaar blijven. Ze worden uiteindelijk verwijderd en worden nooit geteld als onderdeel van de opslag van de gebruiker of er worden ook kosten in rekening gebracht. Afhankelijk van de taak logica van de klant kunnen deze problemen veroorzaken. Als de taak bijvoorbeeld alle bestanden in de map inventariseert en vervolgens bestands lijsten vergelijkt, kan dit mislukken omdat er onverwachte tijdelijke bestanden aanwezig zijn. Als een stroomafwaartse taak alle bestanden uit een bepaalde map inventariseert voor verdere verwerking, kan deze ook de tijdelijke bestanden opsommen.  

    **Oplossing**: er wordt een oplossing geïdentificeerd in de runtime waarbij de tijdelijke bestanden worden opgeslagen in de tijdelijke map op account niveau dan de huidige uitvoermap. De tijdelijke bestanden worden geschreven in deze nieuwe tijdelijke map en worden verwijderd aan het einde van de taak uitvoering.  
    Omdat deze oplossing de klant gegevens verwerkt, is het uiterst belang rijk om deze oplossing goed te valideren binnen MSFT voordat deze wordt uitgebracht. In het midden van het jaar 2021 en als standaard runtime in de tweede helft van het jaar 2021 wordt deze oplossing naar verwachting als bèta runtime beschikbaar gesteld. 


## <a name="see-also"></a>Zie ook

- [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Taken in Azure Data Lake Analytics bewaken met behulp van de Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
