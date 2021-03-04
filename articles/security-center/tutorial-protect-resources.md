---
title: Zelfstudie voor toegang en besturingselementen - Azure Security Center
description: In deze zelfstudie leert u hoe u een Just-In-Time-VM-toegangsbeleid en een toepassingsbeheerbeleid configureert.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: cc88561f8882b1712648a261d1f0208a18bab26d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095473"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Zelfstudie: Uw resources beveiligen met Azure Security Center
Security Center beperkt de blootstelling aan bedreigingen met behulp van toegangs- en toepassingsbesturingselementen om schadelijke activiteiten te blokkeren. Just-In-Time-toegang (JIT) tot virtuele machines (VM's) vermindert de blootstelling aan aanvallen doordat u permanente toegang tot VM's kunt weigeren. U biedt in plaats daarvan beheerde en gecontroleerde toegang tot VM's, alleen wanneer dat nodig is. Besturingselementen voor adaptieve toepassingen helpen u om VM's beter te beschermen tegen malware door te beheren welke toepassingen op uw VM's kunnen worden uitgevoerd. Security Center maakt gebruik van machine learning om de processen te analyseren die op de virtuele machine worden uitgevoerd. Ook helpt het u op basis van deze informatie regels voor opname in de acceptatielijst toe te passen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Just-In-Time-VM-toegangsbeleid configureren
> * Een toepassingsbeheerbeleid configureren

## <a name="prerequisites"></a>Vereisten
Om de functies in deze zelfstudie te doorlopen, moet Azure Defender zijn ingeschakeld. Er is een gratis proefversie beschikbaar. Zie [Azure Defender inschakelen](enable-azure-defender.md)als u een upgrade wilt uitvoeren.

## <a name="manage-vm-access"></a>VM-toegang beheren
JIT-VM-toegang kan worden gebruikt om binnenkomend verkeer naar uw Azure-VM's te blokkeren, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig toegang wordt geboden om verbinding met VM's te kunnen maken wanneer dat nodig is.

Beheerpoorten hoeven niet te allen tijde geopend te zijn. Ze hoeven alleen geopend te zijn wanneer u bent verbonden met de VM, bijvoorbeeld om beheer- of onderhoudstaken uit te voeren. Wanneer Just-In-Time is ingeschakeld, maakt Security Center gebruik van NSG-regels (netwerkbeveiligingsgroep) die toegang tot beheerpoorten beperken, zodat ze niet het doelwit van aanvallers worden.

Volg hiervoor de instructies in [Uw beheerpoorten beveiligen met Just-In-Time-toegang](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Virtuele machines beschermen tegen malware
Met Besturingselementen voor adaptieve toepassingen kunt u een set toepassingen definiëren die in de geconfigureerde resourcegroepen mogen worden uitgevoerd. Een van de voordelen hiervan is dat uw VM's tegen malware worden beschermd. Security Center maakt gebruik van machine learning om de processen te analyseren die op de virtuele machine worden uitgevoerd. Ook helpt het u op basis van deze informatie regels voor opname in de acceptatielijst toe te passen.

Volg de instructies in [Adaptieve toepassingsregelaars gebruiken om de kwetsbaarheid voor aanvallen van uw machines te verminderen](security-center-adaptive-application.md).

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u blootstelling aan bedreigingen kunt beperken met de volgende handelingen:

> [!div class="checklist"]
> * Een Just-In-Time-VM-toegangsbeleid configureren om beheerde en gecontroleerde toegang tot VM's te bieden, alleen wanneer dat nodig is
> * Een beleid voor besturingselementen voor adaptieve toepassingen configureren om te bepalen welke toepassingen op uw VM's kunnen worden uitgevoerd

Ga naar de volgende zelfstudie voor meer informatie over het reageren op beveiligingsincidenten.

> [!div class="nextstepaction"]
> [Zelfstudie: Reageren op beveiligingsincidenten](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
