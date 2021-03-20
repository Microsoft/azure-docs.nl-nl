---
title: Overzicht van Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Meer informatie over de StorSimple Data Manager-oplossing en hoe u deze service kunt gebruiken om toepassingen te schrijven die gebruikmaken van StorSimple-gegevens en andere Azure-Services.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88183493"
---
# <a name="storsimple-data-manager-solution-overview"></a>Overzicht van StorSimple Data Manager oplossingen

## <a name="overview"></a>Overzicht

Microsoft Azure StorSimple gebruikt cloud opslag als een uitbrei ding van de on-premises oplossing en maakt automatisch gebruik van gegevens in on-premises opslag en de Cloud. Gegevens worden opgeslagen in de cloud in een ontdubbeling en gecomprimeerde indeling voor maximale efficiëntie en lagere kosten. Omdat de gegevens worden opgeslagen in de StorSimple-indeling, kan deze niet eenvoudig worden gebruikt door andere Cloud toepassingen die u wilt gebruiken.

Met de StorSimple Data Manager kunt u probleemloos toegang krijgen tot de StorSimple-indelings gegevens in de Cloud. Dit wordt gedaan door de StorSimple-indeling te transformeren naar systeem eigen blobs en bestanden, die u kunt gebruiken met andere services, zoals Azure Media Services, Azure HDInsights en Azure Machine Learning.

Dit artikel bevat een overzicht van de StorSimple Data Manager-oplossing. Ook wordt uitgelegd hoe u deze service kunt gebruiken om toepassingen te schrijven die gebruikmaken van StorSimple-gegevens en andere Azure-Services in de Cloud.

## <a name="how-it-works"></a>Hoe werkt het?

De StorSimple Data Manager-service identificeert StorSimple-gegevens in de cloud van een on-premises 8000 StorSimple-serie. De StorSimple-gegevens in de cloud worden ontdubbeld, gecomprimeerde StorSimple-indeling. De Data Manager-service biedt Api's om de StorSimple-indelings gegevens te extra heren en te transformeren naar andere indelingen, zoals Azure-blobs en Azure Files. Deze getransformeerde gegevens worden vervolgens gemakkelijk door Azure HDInsight en Azure Media Services gebruikt. De gegevens transformatie zorgt er daarom voor dat deze services kunnen worden gebruikt voor de getransformeerde StorSimple-gegevens van een on-premises apparaat uit de 8000 StorSimple-serie. Deze stroom wordt geïllustreerd in het volgende diagram.

![Diagram op hoog niveau](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager use cases

U kunt de Data Manager met Azure Functions, Azure Automation en Azure Data Factory gebruiken om werk stromen op uw gegevens uit te voeren zoals die in StorSimple worden geleverd. U kunt uw media-inhoud die u opslaat op StorSimple met Azure Media Services, of een Machine Learning-algoritme uitvoeren op die gegevens, of een Hadoop-cluster openen om de gegevens te analyseren die u in StorSimple opslaat. Met de enorme matrix van services die beschikbaar zijn op Azure, gecombineerd met de gegevens op StorSimple, kunt u de kracht van uw gegevens ontgrendelen.


## <a name="region-availability"></a>Beschikbaarheid in regio’s

De StorSimple Data Manager is beschikbaar in de volgende 7 regio's:

 - Azië - zuidoost
 - VS - oost
 - VS - west
 - VS - west 2
 - VS - west-centraal
 - Europa - noord
 - Europa -west

De StorSimple Data Manager kan echter worden gebruikt voor het transformeren van gegevens in de volgende regio's. 

![Beschik bare regio's voor gegevens](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Deze set is groter omdat de resource-implementatie in een van de bovenstaande regio's in staat is om het transformatie proces in de onderstaande regio's in te zetten. Zo lang uw gegevens zich in een van de 19 regio's bevinden, kunt u uw gegevens transformeren met behulp van deze service.


## <a name="choosing-a-region"></a>Een regio kiezen

We raden aan dat:
 - Uw bron-en opslag account (dat is gekoppeld aan uw StorSimple-apparaat) en het doel-opslag account (waar u de gegevens in de oorspronkelijke indeling) zich in dezelfde Azure-regio bevinden.
 - U brengt uw Data Manager en taak definitie in de regio die het StorSimple-opslag account bevat. Als dit niet mogelijk is, brengt u de Data Manager in de dichtstbijzijnde Azure-regio en maakt u de taak definitie in dezelfde regio als uw StorSimple-opslag account. 

    Als uw StorSimple-opslag account zich niet in de 26 regio's bevindt die het maken van de taak definitie ondersteunen, raden we u aan om StorSimple Data Manager niet uit te voeren omdat u lange wacht tijden en mogelijke uitzonderings kosten ziet.
    
Micro soft streeft ernaar om ervoor te zorgen dat Azure-Services altijd beschikbaar zijn in alle regio's. Ongeplande service storingen kunnen echter voor korte Peri Oden in een bepaalde regio optreden. In dergelijke gevallen kunt u een Data Manager en taak definitie weer geven in een regio die niet wordt beïnvloed door de storing en de transformatie taak uitvoert. In een dergelijk scenario kan er een extra latentie optreden, maar dit kan uw herstel strategie zijn in zeldzame gevallen van een regionale storing.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

De StorSimple Data Manager moet de versleutelings sleutel voor service gegevens transformeren van de StorSimple-indeling naar de systeem eigen indeling. De versleutelings sleutel voor service gegevens wordt gegenereerd wanneer het eerste apparaat wordt geregistreerd bij de StorSimple-service. Ga naar [StorSimple-beveiliging](storsimple-8000-security.md)voor meer informatie over deze sleutel.

De versleutelings sleutel voor service gegevens die is opgegeven als invoer, wordt opgeslagen in een sleutel kluis die wordt gemaakt wanneer u een Data Manager maakt. De kluis bevindt zich in dezelfde Azure-regio als uw StorSimple Data Manager. Deze sleutel wordt verwijderd wanneer u uw Data Manager-service verwijdert.

Deze sleutel wordt gebruikt door de reken resources om de trans formatie uit te voeren. Deze reken resources bevinden zich in dezelfde Azure-regio als uw taak definitie. Deze regio kan of mag niet hetzelfde zijn als de regio waar u uw Data Manager opneemt.

Als uw Data Manager regio afwijkt van de taak definitie regio, is het belang rijk dat u begrijpt welke gegevens/meta gegevens zich in elk van deze regio's bevinden. Het volgende diagram illustreert het effect van het gebruik van verschillende regio's voor Data Manager en taak definitie.

![Service-en taak definitie in verschillende regio's](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Persoonlijke gegevens beheren

Met de StorSimple Data Manager worden geen persoonlijke gegevens verzameld of weer gegeven. Bekijk het Microsoft-privacybeleid in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter) voor meer informatie.

## <a name="known-limitations"></a>Bekende beperkingen

De service heeft momenteel de volgende beperkingen:
- De StorSimple Data Manager werkt momenteel niet met volumes die zijn versleuteld met BitLocker. Er worden taak fouten weer geven als u probeert om de service uit te voeren met een versleuteld station.
- Sommige meta gegevens van bestanden (inclusief Acl's) worden niet in de getransformeerde gegevens bewaard.
- Deze service werkt alleen met NTFS-volumes.
- De lengte van het bestandspad moet kleiner zijn dan 256 tekens anders kan de taak niet worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

[Gebruik StorSimple Data Manager gebruikers interface om uw gegevens te transformeren](storsimple-data-manager-ui.md).
