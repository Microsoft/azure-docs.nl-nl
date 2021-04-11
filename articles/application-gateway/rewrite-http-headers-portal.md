---
title: HTTP-aanvraag-en reactie headers opnieuw schrijven in portal-Azure-toepassing gateway
description: Meer informatie over het gebruik van de Azure Portal om een Azure-toepassing gateway te configureren voor het herschrijven van de HTTP-headers in de aanvragen en antwoorden die via de gateway worden door gegeven
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708931"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-aanvraag-en-antwoord headers herschrijven met Azure-toepassing gateway-Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om een SKU-exemplaar van [Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) te configureren om de HTTP-headers in aanvragen en antwoorden te herschrijven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

U moet een SKU-exemplaar van Application Gateway v2 hebben om de stappen in dit artikel te kunnen volt ooien. Het opnieuw schrijven van headers wordt niet ondersteund in de V1-SKU. Als u de v2-SKU niet hebt, maakt u een [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) -exemplaar voordat u begint.

## <a name="create-required-objects"></a>Vereiste objecten maken

Als u het opnieuw schrijven van HTTP-headers wilt configureren, moet u deze stappen volt ooien.

1. De objecten maken die vereist zijn voor het herschrijven van HTTP-headers:

   - **Herschrijf actie**: wordt gebruikt om de velden aanvraag en aanvraag header op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U kunt een of meer herschrijf voorwaarden koppelen met een herschrijf actie.

   - **Voor waarde voor herschrijven**: een optionele configuratie. Bij herschrijf voorwaarden wordt de inhoud van HTTP (S)-aanvragen en-antwoorden geëvalueerd. De actie herschrijven vindt plaats als de HTTP (S)-aanvraag of het antwoord overeenkomt met de voor waarde voor herschrijven.

     Als u meer dan één voor waarde aan een actie koppelt, treedt de actie alleen op wanneer aan alle voor waarden wordt voldaan. Met andere woorden, de bewerking is een logische en-bewerking.

   - **Regel voor herschrijven**: bevat meerdere combi Naties van actie voor opnieuw schrijven/voor waarden herschrijven.

   - **Regel volgorde**: helpt bij het bepalen van de volg orde waarin de regels voor herschrijven worden uitgevoerd. Deze configuratie is handig wanneer u meerdere regels voor herschrijven hebt opgegeven in een herschrijfset. Een regel voor herschrijven met een lagere regel reeks waarde wordt eerst uitgevoerd. Als u dezelfde regel reeks waarde toewijst aan twee regels voor herschrijven, is de volg orde van uitvoering niet-deterministisch.

   - **Set opnieuw schrijven**: bevat meerdere herschrijf regels die worden gekoppeld aan een regel voor het door sturen van aanvragen.

2. Koppel de herschrijfset aan een routerings regel. De herschrijf configuratie wordt gekoppeld aan de bron-listener via de routerings regel. Wanneer u een basis regel voor door sturen gebruikt, wordt de configuratie voor het opnieuw schrijven van kopteksten gekoppeld aan een bronhost en is het herschrijven van de globale header. Wanneer u een op pad gebaseerde routerings regel gebruikt, wordt de configuratie voor het herschrijven van kopteksten gedefinieerd op de URL-pad toewijzing. In dat geval geldt dit alleen voor het specifieke pad van een site.

U kunt meerdere herschrijf sets voor HTTP-headers maken en elke herschrijfset Toep assen op meerdere listeners. Maar u kunt slechts één herschrijfset Toep assen op een specifieke listener.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

## <a name="configure-header-rewrite"></a>Herschrijven van header configureren

In dit voor beeld wijzigen we een omleidings-URL door de locatie header te herschrijven in het HTTP-antwoord dat door een back-end-toepassing is verzonden.

1. Selecteer **alle resources** en selecteer vervolgens uw toepassings gateway.

2. Selecteer **herschrijf bewerkingen** in het linkerdeel venster.

3. Selecteer **set herschrijven**:

   ![Set voor herschrijven toevoegen](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Geef een naam op voor de set voor herschrijven en koppel deze aan een routerings regel:

   - Voer de naam in voor de herschrijfset in het vak **naam** .
   - Selecteer een of meer regels die worden vermeld in de lijst **gekoppelde routerings regels** . U kunt alleen regels selecteren die niet zijn gekoppeld aan andere herschrijf sets. De regels die al zijn gekoppeld aan andere herschrijf sets, worden grijs weer gegeven.
   - Selecteer **Next**.
   
     ![Naam en koppeling toevoegen](media/rewrite-http-headers-portal/name-and-association.png)

5. Een herschrijf regel maken:

   - Selecteer **regel voor herschrijven toevoegen**.

     ![Regel voor herschrijven toevoegen](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Voer een naam in voor de regel voor herschrijven in het vak **regel naam herschrijven** . Voer een getal in het vak **regel reeks** in.

     ![Regel naam voor herschrijven toevoegen](media/rewrite-http-headers-portal/rule-name.png)

6. In dit voor beeld wordt de locatie header alleen opnieuw geschreven wanneer deze een verwijzing naar azurewebsites.net bevat. Als u dit wilt doen, voegt u een voor waarde toe om te evalueren of de locatie header in het antwoord azurewebsites.net bevat:

   - Selecteer **voor waarde toevoegen** en selecteer vervolgens het vak met de **if** -instructies om het uit te vouwen.

     ![Een voorwaarde toevoegen](media/rewrite-http-headers-portal/add-condition.png)

   - Selecteer in de lijst **type variabele die u wilt controleren** de optie **http-header**.

   - Selecteer in de lijst **header type** de optie **Response**.

   - Omdat we in dit voor beeld de locatie header evalueren, een veelgebruikte header, selecteert u  **algemene header** onder **header naam**.

   - Selecteer in de lijst **common header** de optie **locatie**.

   - Onder **hoofdletter gevoelig** selecteert u **Nee**.

   - Selecteer in de lijst **operator** de optie **gelijk aan (=)**.

   - Geef een reguliere-expressie patroon op. In dit voor beeld gebruiken we het patroon `(https?)://.*azurewebsites.net(.*)$` .

   - Selecteer **OK**.

     ![Een if-voor waarde configureren](media/rewrite-http-headers-portal/condition.png)

7. Voeg een actie toe om de locatie header opnieuw te schrijven:

   - In de lijst **actie type** selecteert u **instellen**.

   - Selecteer in de lijst **header type** de optie **Response**.

   - Selecteer onder **koptekst naam** de optie **common header**.

   - Selecteer in de lijst **common header** de optie **locatie**.

   - Voer de waarde voor de header in. In dit voor beeld gebruiken we `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als waarde voor de header. Met deze waarde wordt *azurewebsites.net* vervangen door *contoso.com* in de locatie header.

   - Selecteer **OK**.

     ![Een actie toevoegen](media/rewrite-http-headers-portal/action.png)

8. Selecteer **maken** om de set voor opnieuw schrijven te maken:

   ![Selecteer Maken](media/rewrite-http-headers-portal/create.png)

9. De weer gave set opnieuw schrijven wordt geopend. Controleer of de set die u hebt gemaakt, in de lijst met herschrijf sets staat:

   ![Weer gave set opnieuw schrijven](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Volgende stappen

Zie [algemene scenario's voor het herschrijven van kopteksten](./rewrite-http-headers.md)voor meer informatie over het instellen van veelvoorkomende use cases.