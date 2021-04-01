---
title: URL en query reeks opnieuw schrijven met Azure-toepassing gateway-Azure Portal
description: Meer informatie over het gebruik van de Azure Portal om een Azure-toepassing gateway te configureren voor het herschrijven van de URL en de query reeks
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: ec58c6f97efdbcb91071bcea98bbbc614833246d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215770"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>URL opnieuw schrijven met Azure-toepassing gateway-Azure Portal (preview-versie)

In dit artikel wordt beschreven hoe u met behulp van de Azure Portal een SKU-exemplaar van [Application Gateway v2](application-gateway-autoscaling-zone-redundant.md) kunt configureren om de URL opnieuw te schrijven.

>[!NOTE]
> De functie voor het herschrijven van URL'S is in Preview en is alleen beschikbaar voor Standard_v2 en WAF_v2 SKU van Application Gateway. Het wordt niet aanbevolen voor gebruik in een productie omgeving. Zie hier voor meer informatie over het [gebruik van voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)beelden.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

U moet een SKU-exemplaar van Application Gateway v2 hebben om de stappen in dit artikel te kunnen volt ooien. Het herschrijven van de URL wordt niet ondersteund in de V1-SKU. Als u de v2-SKU niet hebt, maakt u een [Application Gateway v2 SKU](tutorial-autoscale-ps.md) -exemplaar voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

## <a name="configure-url-rewrite"></a>Opnieuw genereren van URL configureren

In het onderstaande voor beeld wanneer de aanvraag-URL */article* bevat, worden het URL-pad en de URL-query teken reeks herschreven

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Selecteer **alle resources** en selecteer vervolgens uw toepassings gateway.

2. Selecteer **herschrijf bewerkingen** in het linkerdeel venster.

3. Selecteer **set herschrijven**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Set voor herschrijven toevoegen":::

4. Geef een naam op voor de set voor herschrijven en koppel deze aan een routerings regel:

    a. Voer de naam in voor de herschrijfset in het vak **naam** .
    
    b. Selecteer een of meer regels die worden vermeld in de lijst **gekoppelde routerings regels** . Dit wordt gebruikt om de herschrijf configuratie aan de bron-listener te koppelen via de routerings regel. U kunt alleen die routerings regels selecteren die niet zijn gekoppeld aan andere herschrijf sets. De regels die al zijn gekoppeld aan andere herschrijf sets, worden grijs weer gegeven.
    
    c. Selecteer **Volgende**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Koppelen aan een regel":::

5. Een herschrijf regel maken:

    a. Selecteer **regel voor herschrijven toevoegen**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Scherm afbeelding die de regel voor herschrijven toevoegen markeert.":::
    
    b. Voer een naam in voor de regel voor herschrijven in het vak **regel naam herschrijven** . Voer een getal in het vak **regel reeks** in.

6. In dit voor beeld wordt het URL-pad en de URL-query teken reeks alleen opnieuw geschreven wanneer het pad */article* bevat. Als u dit wilt doen, voegt u een voor waarde toe om te evalueren of het URL-pad */article* bevat

    a. Selecteer **voor waarde toevoegen** en selecteer vervolgens het vak met de **if** -instructies om het uit te vouwen.
    
    b. Omdat we in dit voor beeld het patroon */article* in het URL-pad willen controleren, selecteert u in de lijst **type te controleren variabele** de optie **server variabele**.
    
    c. Selecteer in de lijst **Server variabelen** uri_path
    
    d. Onder **hoofdletter gevoelig** selecteert u **Nee**.
    
    e. Selecteer in de lijst **operator** de optie **gelijk aan (=)**.
    
    f. Geef een reguliere-expressie patroon op. In dit voor beeld gebruiken we het patroon `.*article/(.*)/(.*)`
    
      () wordt gebruikt voor het vastleggen van de subtekenreeks voor later gebruik bij het opstellen van de expressie voor het herschrijven van het URL-pad. Klik [hier](rewrite-http-headers-url.md#capturing) voor meer informatie.

    g. Selecteer **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. Een actie toevoegen om het URL-en URL-pad opnieuw te schrijven

   a. Selecteer in de lijst **type herschrijven** de **URL**.

   b. In de lijst **actie type** selecteert u **instellen**.

   c. Selecteer onder **onderdelen** de optie **URL-pad en URL-query reeks**

   d. Voer in de waarde van het **URL-pad** de nieuwe waarde van het pad in. In dit voor beeld gebruiken we **/article.aspx** 

   e. Voer in de **URL-query teken reeks waarde** de nieuwe waarde van de URL-query teken reeks in. In dit voor beeld gebruiken we **id = {var_uri_path_1} &titel = {var_uri_path_2}**
    
    `{var_uri_path_1}` en `{var_uri_path_1}` worden gebruikt voor het ophalen van de subtekenreeksen die zijn vastgelegd tijdens het evalueren van de voor waarde in deze expressie `.*article/(.*)/(.*)`
    
   f. Selecteer **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Actie":::

8. Klik op **maken** om de set voor opnieuw schrijven te maken.

9. Controleren of de nieuwe set opnieuw schrijven wordt weer gegeven in de lijst met herschrijf sets

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Regel voor herschrijven toevoegen":::

## <a name="verify-url-rewrite-through-access-logs"></a>URL-herschrijven controleren via toegangs logboeken

Bekijk de onderstaande velden in Access-Logboeken om te controleren of de herschrijf bewerking van de URL is gebeurd volgens uw verwachtingen.

* **originalRequestUriWithArgs**: dit veld bevat de oorspronkelijke aanvraag-URL
* **requestUri**: dit veld bevat de URL na de herschrijf bewerking op Application Gateway

Zie [hier](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)voor meer informatie over alle velden in de logboeken van de toegang.

##  <a name="next-steps"></a>Volgende stappen

Zie [algemene scenario's voor herschrijven](rewrite-http-headers.md)voor meer informatie over het instellen van herschrijven voor enkele veelvoorkomende use cases.
