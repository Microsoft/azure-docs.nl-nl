---
title: 'Zelfstudie: WAF-beleid maken voor Azure Front Door - Azure Portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) kunt maken met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: victorh
ms.openlocfilehash: 8b1d1007e817bafe3d75f0f0d7c3fc6eb5470854
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729467"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Zelfstudie: Een Web Application Firewall-beleid maken op Azure Front Door met behulp van de Azure Portal

In deze zelfstudie leert u hoe u een basis Azure Web Application Firewall-beleid (WAF) kunt maken en kunt toepassen op een frontend-host bij Azure Front Door.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Dit koppelen aan een frontend-host
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Front Door-profiel door de instructies op te volgen in [Quickstart: Een Front Door-profiel maken](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken

Maak eerst een basis WAF-beleid met beheerde standaardregelset (DRS) met behulp van de portal. 

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken**> zoek naar **WAF**> selecteer **Web Application Firewall (Preview)** > selecteer **Maken**.
2. Voer op het tabblad **Basis** van de pagina **WAF-beleid maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement            |Selecteer de naam van uw Front Door-abonnement.|
    | Resourcegroep          |Selecteer de naam van uw Front Door-resourcegroep.|
    | Beleidsnaam             |Voer een unieke naam voor uw WAF-beleid in.|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Schermopname van de pagina een W A F-beleid met een knop Beoordelen en maken en keuzelijsten voor het abonnement, de resourcegroep en beleidsnaam" border="false":::.

3. Selecteer op het tabblad **Koppeling** van de pagina **Een WAF-beleid maken** de optie **Frontend-host toevoegen**, voer de volgende instellingen in en selecteer **Toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Front Door              | Selecteer de naam van uw Front Door-profiel.|
    | Frontend-host           | Selecteer de naam van de Front Door-host en selecteer vervolgens **Toevoegen**.|
    
    > [!NOTE]
    > Als de frontend-host aan een WAF-beleid is gekoppeld, wordt deze grijs weergegeven. U moet eerst de frontend-host uit het bijbehorende beleid verwijderen en vervolgens de frontend-host opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="configure-web-application-firewall-rules-optional"></a>Web Application Firewall-regels configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het WAF-beleid zich standaard in de modus **Detectie**. In de modus **Detectie** blokkeert WAF geen aanvragen, maar worden aanvragen die overeenkomen met de WAF-regels vastgelegd in WAF-logboeken.
Als u WAF in actie wilt zien, kunt u de modusinstellingen van **Detectie** wijzigen in **Preventie**. In de modus **Preventie** worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (DRS) geblokkeerd en vastgelegd in WAF-logboeken.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Schermopname van de sectie Beleidsinstellingen. De wisselknop Modus is ingesteld op Preventie." border="false":::

### <a name="custom-rules"></a>Aangepaste regels

U kunt een aangepaste regel maken door **Aangepaste regel toevoegen** onder het gedeelte **Aangepaste regels** te selecteren. Hiermee opent u de pagina voor de configuratie van aangepaste regels. Hieronder ziet u een voorbeeld van het configureren van een aangepaste regel voor het blokkeren van een aanvraag als de queryreeks **blockme** bevat.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Schermopname van de pagina voor configuratie van aangepaste regels, met de instellingen voor een regel waarmee wordt gecontroleerd of de variabele QueryString de waarde blockme bevat." border="false":::

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaardregelset die door Azure wordt beheerd, is standaard ingeschakeld. De huidige standaard versie is DefaultRuleSet_1.0. Van WAF **beheerde regels**, **toewijzen**, onlangs beschik bare ruleset Microsoft_DefaultRuleSet_1 is beschikbaar in de vervolg keuzelijst.

Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het **selectievakje** vóór het regelnummer in en selecteert u **Uitschakelen** op het bovenstaande tabblad. Als u de actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje vóór het regelnummer in en selecteert u vervolgens het bovenstaande tabblad **Actie wijzigen**.

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="Schermopname van de pagina Beheerde regels, met een regelset, regelgroepen, regels en de knoppen Inschakelen, Uitschakelen en Actie wijzigen. Er is één regel ingeschakeld." border="false":::

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Front Door](../../frontdoor/front-door-overview.md)
