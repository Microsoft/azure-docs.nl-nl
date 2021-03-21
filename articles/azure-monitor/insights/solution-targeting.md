---
title: Bewakings oplossingen richten in Azure Monitor | Microsoft Docs
description: Met behulp van doel-en bewakings oplossingen kunt u de bewakings oplossingen beperken tot een specifieke set agents.  In dit artikel wordt beschreven hoe u een scope configuratie maakt en toepast op een oplossing.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728566"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Doelen voor bewakings oplossingen in Azure Monitor (preview-versie)
Wanneer u een bewakings oplossing aan uw abonnement toevoegt, wordt deze automatisch standaard geïmplementeerd voor alle Windows-en Linux-agents die zijn verbonden met uw Log Analytics-werk ruimte.  Mogelijk wilt u uw kosten beheren en het aantal verzamelde gegevens voor oplossing beperken door deze te beperken tot een bepaalde set agents.  In dit artikel wordt beschreven hoe u **doel items van oplossingen** kunt gebruiken. Dit is een functie waarmee u een bereik kunt Toep assen op uw oplossingen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Een oplossing richten
Er zijn drie stappen voor het richten op een oplossing zoals beschreven in de volgende secties. 


### <a name="1-create-a-computer-group"></a>1. een computer groep maken
U geeft de computers op die u wilt gebruiken in een bereik door een [computer groep](../logs/computer-groups.md) te maken in azure monitor.  De computer groep kan worden gebaseerd op een logboek query of worden geïmporteerd uit andere bronnen, zoals Active Directory-of WSUS-groepen. Zoals [hieronder wordt beschreven](#solutions-and-agents-that-cant-be-targeted), worden alleen computers die rechtstreeks zijn verbonden met Azure monitor, opgenomen in het bereik.

Zodra u de computer groep hebt gemaakt in uw werk ruimte, neemt u deze op in een bereik configuratie die kan worden toegepast op een of meer oplossingen.
 
 
### <a name="2-create-a-scope-configuration"></a>2. een scope configuratie maken
 Een **Scope configuratie** bevat een of meer computer groepen en kan worden toegepast op een of meer oplossingen. 
 
 Maak een scope configuratie met behulp van het volgende proces.  

 1. Navigeer in het Azure Portal naar **log Analytics werk ruimten** en selecteer uw werk ruimte.
 2. Selecteer in de eigenschappen van de werk ruimte onder **gegevens bronnen voor werk ruimte** **Scope configuraties**.
 3. Klik op **toevoegen** om een nieuwe scope configuratie te maken.
 4. Typ een **naam** voor de scope configuratie.
 5. Klik op **computer groepen selecteren**.
 6. Selecteer de computer groep die u hebt gemaakt en eventueel alle andere groepen die u wilt toevoegen aan de configuratie.  Klik op **Selecteren**.  
 6. Klik op **OK** om de scope configuratie te maken. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Pas de scope configuratie toe op een oplossing.
Zodra u een scope configuratie hebt, kunt u deze Toep assen op een of meer oplossingen.  Houd er rekening mee dat een configuratie met één scope kan worden gebruikt met meerdere oplossingen, maar voor elke oplossing kan slechts één scope configuratie worden gebruikt.

Pas een scope configuratie toe met behulp van het volgende proces.  

 1. Navigeer in het Azure Portal naar **log Analytics werk ruimten** en selecteer uw werk ruimte.
 2. Selecteer in de eigenschappen van de werk ruimte de optie **oplossingen**.
 3. Klik op de oplossing die u wilt bereiken.
 4. In de eigenschappen voor de oplossing onder **werkruimte gegevens bronnen** selecteert u **oplossings doelen**.  Als de optie niet beschikbaar is, [kan deze oplossing niet worden benaderd](#solutions-and-agents-that-cant-be-targeted).
 5. Klik op **Scope configuratie toevoegen**.  Als u al een configuratie op deze oplossing hebt toegepast, is deze optie niet beschikbaar.  U moet de bestaande configuratie verwijderen voordat u er een toevoegt.
 6. Klik op de bereik configuratie die u hebt gemaakt.
 7. Bekijk de **status** van de configuratie om er zeker van te zijn dat deze **correct** wordt weer gegeven.  Als de status een fout aangeeft, klikt u op het weglatings teken rechts van de configuratie en selecteert u **Scope configuratie bewerken** om wijzigingen aan te brengen.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Oplossingen en agents die niet bedoeld zijn
Hieronder vindt u de criteria voor agents en oplossingen die niet kunnen worden gebruikt voor het doel van oplossingen.

- Doel stellingen van oplossingen zijn alleen van toepassing op oplossingen die worden geïmplementeerd op agents.
- Doel stellingen van oplossingen zijn alleen van toepassing op door micro soft verzorgde oplossing.  Het is niet van toepassing op oplossingen [die zijn gemaakt door uzelf of partners](./solutions.md).
- U kunt alleen agents uitfilteren die rechtstreeks verbinding maken met Azure Monitor.  Oplossingen worden automatisch geïmplementeerd op agents die deel uitmaken van een verbonden Operations Manager-beheer groep, ongeacht of ze zijn opgenomen in een scope configuratie.

### <a name="exceptions"></a>Uitzonderingen
Doel stellingen van oplossingen kunnen niet worden gebruikt met de volgende oplossingen, zelfs als ze voldoen aan de opgegeven criteria.

- Status van agent beoordeling

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het bewaken van oplossingen, inclusief de oplossingen die beschikbaar zijn voor installatie in uw omgeving, bij het [toevoegen van Azure log Analytics bewakings oplossingen voor uw werk ruimte](solutions.md).
- Meer informatie over het maken van computer groepen bij [computer groepen in azure monitor-logboek query's](../logs/computer-groups.md).
