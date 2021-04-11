---
title: 'Zelfstudie: WAF-beleid maken voor Azure Front Door - Azure Portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) kunt maken met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122245"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Zelfstudie: Een Web Application Firewall-beleid maken op Azure Front Door met behulp van de Azure Portal

In deze zelfstudie leert u hoe u een basis Azure Web Application Firewall-beleid (WAF) kunt maken en kunt toepassen op een frontend-host bij Azure Front Door.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Dit koppelen aan een frontend-host
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Een [voor deur](../../frontdoor/quickstart-create-front-door.md) of een [Standaard profiel voor de voor deur](../../frontdoor/standard-premium/create-front-door-portal.md) maken. 

## <a name="create-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken

Maak eerst een basis WAF-beleid met beheerde standaardregelset (DRS) met behulp van de portal. 

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken** > zoek naar **WAF** > Selecteer **Web Application firewall (WAF)** > Selecteer **Create**.

1. Voer op het tabblad **Basis** van de pagina **WAF-beleid maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Beleid voor              | Selecteer **globale WAF (front-deur)**. |
    | SKU voor voor deur          | Selecteer een Basic-, Standard-en Premium-SKU. |
    | Abonnement            | Selecteer de naam van uw Front Door-abonnement.|
    | Resourcegroep          | Selecteer de naam van uw Front Door-resourcegroep.|
    | Beleidsnaam             | Voer een unieke naam voor uw WAF-beleid in.|
    | Beleidsstatus            | Ingesteld als **ingeschakeld**. | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Schermopname van de pagina een W A F-beleid met een knop Beoordelen en maken en keuzelijsten voor het abonnement, de resourcegroep en beleidsnaam":::.

1. Op het tabblad **koppeling** van de pagina **een WAF-beleid maken** , selecteert u **+ een voor deur profiel koppelen**, voert u de volgende instellingen in en selecteert u **toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Voorste deur profiel              | Selecteer de naam van uw Front Door-profiel. |
    | Domains          | Selecteer de domeinen waaraan u het WAF-beleid wilt koppelen en selecteer vervolgens **toevoegen**. |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="Scherm afbeelding van de pagina een front-deur profiel koppelen.":::
    
    > [!NOTE]
    > Als het domein is gekoppeld aan een WAF-beleid, wordt dit grijs weer gegeven. U moet eerst het domein verwijderen uit het bijbehorende beleid en vervolgens het domein opnieuw koppelen aan een nieuw WAF-beleid.

1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="configure-web-application-firewall-rules-optional"></a>Web Application Firewall-regels configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het WAF-beleid zich standaard in de modus **Detectie**. In de modus **Detectie** blokkeert WAF geen aanvragen, maar worden aanvragen die overeenkomen met de WAF-regels vastgelegd in WAF-logboeken.
Als u WAF in actie wilt zien, kunt u de modusinstellingen van **Detectie** wijzigen in **Preventie**. In de modus **Preventie** worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (DRS) geblokkeerd en vastgelegd in WAF-logboeken.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Schermopname van de sectie Beleidsinstellingen. De wisselknop Modus is ingesteld op Preventie.":::

### <a name="custom-rules"></a>Aangepaste regels

U kunt een aangepaste regel maken door **Aangepaste regel toevoegen** onder het gedeelte **Aangepaste regels** te selecteren. Hiermee opent u de pagina voor de configuratie van aangepaste regels. 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="Scherm afbeelding van aangepaste regels pagina.":::

Hieronder ziet u een voorbeeld van het configureren van een aangepaste regel voor het blokkeren van een aanvraag als de queryreeks **blockme** bevat.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Schermopname van de pagina voor configuratie van aangepaste regels, met de instellingen voor een regel waarmee wordt gecontroleerd of de variabele QueryString de waarde blockme bevat.":::

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaardregelset die door Azure wordt beheerd, is standaard ingeschakeld. De huidige standaard versie is DefaultRuleSet_1.0. Van WAF **beheerde regels**, **toewijzen**, onlangs beschik bare ruleset Microsoft_DefaultRuleSet_1 is beschikbaar in de vervolg keuzelijst.

Als u een afzonderlijke regel wilt uitschakelen, schakelt u het **selectie vakje** vóór het regel nummer in en selecteert u **uitschakelen** boven aan de pagina. Als u de typen acties voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectie vakje vóór het regel nummer in en selecteert u vervolgens de **wijzigings actie** boven aan de pagina.

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="Scherm afbeelding van de pagina beheerde regels met een regelset, regel groepen, regels, inschakelen, uitschakelen en actie knoppen wijzigen." lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de voor deur](../../frontdoor/front-door-overview.md) 
>  van Azure [Meer informatie over de standaard/Premium van Azure voor de voor deur](../../frontdoor/standard-premium/overview.md)
