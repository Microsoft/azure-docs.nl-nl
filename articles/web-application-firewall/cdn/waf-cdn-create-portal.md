---
title: 'Zelfstudie: WAF-beleid maken voor Azure CDN - Azure Portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) kunt maken op Azure CDN met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 9579d0da3347bdd4ecc627662cee42f909cbfaf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92132768"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Zelfstudie: Een WAF-beleid maken op Azure CDN met behulp van de Azure Portal

In deze zelfstudie leert u hoe u een basis Azure Web Application Firewall-beleid (WAF) kunt maken en kunt toepassen op een eindpunt op Azure Content Delivery Network (CDN).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Dit aan een CDN-eindpunt koppelen. U kunt een WAF-beleid alleen koppelen aan eindpunten die worden gehost op de **Azure CDN Standard van Microsoft** SKU.
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Azure CDN-profiel en -eindpunt door de instructies te volgen in [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken

Maak eerst een basis WAF-beleid met beheerde standaardregelset (DRS) met behulp van de portal.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken**> zoek naar **WAF**> selecteer **Web Application Firewall** > selecteer **Maken**.
2. Voer op het tabblad **Basis** van de pagina **WAF-beleid maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Beleid voor            |Azure CDN selecteren (Preview).|
    | Abonnement            |Selecteer de naam van uw Front Door-abonnement.|
    | Resourcegroep          |Selecteer de naam van uw Front Door-resourcegroep.|
    | Beleidsnaam             |Voer een unieke naam voor uw WAF-beleid in.|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="Schermopname van de pagina Een WAF-beleid maken, met een knop Controleren + maken, en waarden die voor verschillende instellingen zijn ingevoerd." border="false":::

3. Selecteer op het tabblad **Koppeling** van de pagina **Een WAF-beleid maken** de optie **CDN-eindpunt toevoegen**, voer de volgende instellingen in en selecteer **Toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | CDN-profiel              | Selecteer de naam van uw CDN-profiel.|
    | Eindpunt           | Selecteer de naam van uw eindpunt en selecteer vervolgens **Toevoegen**.|
    
    > [!NOTE]
    > Als het eindpunt is gekoppeld aan een WAF-beleid, wordt dit grijs weergegeven. U moet eerst het eindpunt uit het bijbehorende beleid verwijderen en vervolgens het eindpunt opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="configure-web-application-firewall-policy-optional"></a>Web Application Firewall-beleid configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het WAF-beleid zich standaard in de modus *Detectie*. In de modus *Detectie* worden er door WAF geen aanvragen geblokkeerd. Aanvragen die overeenkomen met de WAF-regels worden in plaats daarvan vastgelegd in WAF-logboeken.

Als u WAF in actie wilt zien, kunt u de modusinstellingen van *Detectie* wijzigen in *Preventie*. In de modus *Preventie* worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (DRS) geblokkeerd en vastgelegd in WAF-logboeken.

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="Schermopname van de sectie Beleidsinstellingen. De wisselknop Modus is ingesteld op Preventie." border="false":::

### <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **Aangepaste regel toevoegen** onder het gedeelte **Aangepaste regels**. Hiermee opent u de pagina voor de configuratie van aangepaste regels. Er zijn twee soorten aangepaste regels: **overeenkomstregel** en **frequentielimiet** regel.

De volgende schermopname toont een aangepaste overeenkomstregel voor het blokkeren van een aanvraag als de querytekenreeks de waarde **blockme** bevat.

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="Schermopname van de pagina voor configuratie van aangepaste regels, met de instellingen voor een regel waarmee wordt gecontroleerd of de variabele QueryString de waarde blockme bevat." border="false":::

Voor frequentielimietregels zijn twee extra velden vereist: **Frequentielimietduur** en **Drempelwaarde voor frequentielimietlimiet (aanvragen)** zoals wordt weergegeven in het volgende voorbeeld:

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="Schermopname van de pagina voor configuratie van de regel voor de frequentielimiet. Er zijn een keuzelijst Duur frequentielimiet en een vak Drempelwaarde frequentielimiet (aanvragen) zichtbaar." border="false":::

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaardregelset die door Azure wordt beheerd, is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het selectievakje vóór het regelnummer in en selecteert u **Uitschakelen** op het bovenstaande tabblad. Als u de actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje vóór het regelnummer in en selecteert u vervolgens het bovenstaande tabblad **Actie wijzigen**.

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="Schermopname van de pagina Beheerde regels, met een regelset, regelgroepen, regels en de knoppen Inschakelen, Uitschakelen en Actie wijzigen. Er is één regel ingeschakeld." border="false":::

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Web Application Firewall](../overview.md)
