---
title: Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs
description: In dit artikel leest u hoe u beveiligings waarschuwingen kunt weer geven voor een omgeving in DevTest Labs en een passende maat regelen kunt nemen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91308687"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs
Als test gebruiker kunt u nu Azure Security Center waarschuwingen weer geven voor uw test omgevingen. Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval. Meer [informatie over beveiligings waarschuwingen in azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Vereisten
Op dit moment kunt u alleen beveiligings waarschuwingen weer geven voor PaaS-omgevingen (platform as a Service) die zijn geïmplementeerd in uw Lab. Als u deze functie wilt testen of gebruiken, [implementeert u een omgeving in uw Lab](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Beveiligings waarschuwingen voor een omgeving weer geven

1. Op de start pagina van uw Lab selecteert u **beveiligings waarschuwingen** in het menu links. U ziet het aantal beveiligings waarschuwingen (hoog, gemiddeld en laag). Meer informatie over [hoe waarschuwingen worden geclassificeerd](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Beveiligings waarschuwingen-overzicht](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Klik met de rechter muisknop op drie punten (...) in de laatste kolom en selecteer **beveiligings waarschuwingen weer geven**. 

    ![Scherm opname van de pagina beveiligings waarschuwingen met de melding beveiligings waarschuwingen weer geven geselecteerd.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. U vindt meer informatie over de aanbevelingen voor waarschuwingen en Advisor. Meer informatie over [het beheren van en reageren op beveiligings waarschuwingen in azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Beveiligingswaarschuwingen bekijken](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over omgevingen:

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken](devtest-lab-configure-use-public-environments.md)
