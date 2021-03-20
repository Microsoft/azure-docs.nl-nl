---
title: Azure API Management-beleid instellen of bewerken | Microsoft Docs
description: Meer informatie over het instellen of bewerken van Azure API Management-beleid. Deze beleids regels zijn XML-documenten waarin een reeks van inkomende en uitgaande instructies wordt beschreven.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078320"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management-beleid instellen of bewerken

De beleids definitie is een XML-document dat een reeks van binnenkomende en uitgaande instructies beschrijft. De XML kan rechtstreeks worden bewerkt in het definitie venster. U kunt ook een vooraf gedefinieerd beleid selecteren in de lijst die aan de rechter kant van het venster beleid is opgegeven. De instructies die van toepassing zijn op het huidige bereik, worden ingeschakeld en gemarkeerd. Als u op een ingeschakelde instructie klikt, wordt de juiste XML toegevoegd aan de locatie van de cursor in de definitie weergave. 

Zie [beleid in Azure API Management](api-management-howto-policies.md)voor gedetailleerde informatie over beleid.

## <a name="set-or-edit-a-policy"></a>Beleid instellen of bewerken

Voer de volgende stappen uit om een beleid in te stellen of te bewerken:

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Blader naar de APIM-instantie.
3. Klik op het tabblad **API's**.

    ![Beleid bewerken](./media/set-edit-policies/code-editor.png)

4. Selecteer een van de API's die u eerder hebt geïmporteerd.
5. Selecteer het tabblad **Ontwerpen**.
6. Selecteer een bewerking waarop u het beleid wilt Toep assen. Als u het beleid wilt Toep assen op alle bewerkingen, selecteert u **alle bewerkingen**.
7. Selecteer het **</>** pictogram (code-editor) in de sectie **binnenkomende verwerking** of **uitgaande verwerking** .
8. Plak de gewenste beleids code in een van de juiste blokken.

    ```xml
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Bereik configureren

Beleids regels kunnen globaal of op het bereik van een product, API of bewerking worden geconfigureerd. Als u een beleid wilt configureren, moet u eerst het bereik selecteren waarop het beleid van toepassing moet zijn.

Beleids bereiken worden in de volgende volg orde geëvalueerd:

1. Globaal bereik
2. Product bereik
3. API-bereik
4. Bewerkings bereik

De instructies binnen het beleid worden geëvalueerd op basis van de plaatsing van het `base` element, indien aanwezig. Globaal beleid heeft geen bovenliggend beleid en gebruikt het `<base>` element hierin geen effect.

Als u het beleid in het huidige bereik in de beleids editor wilt zien, klikt u op **effectief beleid opnieuw berekenen voor geselecteerd bereik**.

### <a name="global-scope"></a>Globaal bereik

Het globale bereik is geconfigureerd voor **alle api's** in uw APIM-exemplaar.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar uw APIM-exemplaar.
2. Klik op **alle api's**.

    ![Globaal bereik](./media/api-management-howto-policies/global-scope.png)

3. Klik op het pictogram drie hoek.
4. Selecteer **Code-editor**.
5. Beleids regels toevoegen of bewerken.
6. Klik op **Opslaan**. 

    De wijzigingen worden onmiddellijk door gegeven aan de API Management Gateway.

### <a name="product-scope"></a>Product bereik

Het product bereik is geconfigureerd voor het geselecteerde product.

1. Klik op **producten**.

    ![Product bereik](./media/api-management-howto-policies/product-scope.png)

2. Selecteer het product waarvoor u beleids regels wilt Toep assen.
3. Klik op **beleids regels**.
4. Beleids regels toevoegen of bewerken.
5. Klik op **Opslaan**. 

### <a name="api-scope"></a>API-bereik

Het API-bereik is geconfigureerd voor **alle bewerkingen** van de geselecteerde API.

1. Selecteer de **API** waarop u beleids regels wilt Toep assen.

    ![API-bereik](./media/api-management-howto-policies/api-scope.png)

2. Selecteer **Alle bewerkingen**
3. Klik op het pictogram drie hoek.
4. Selecteer **Code-editor**.
5. Beleids regels toevoegen of bewerken.
6. Klik op **Opslaan**. 

### <a name="operation-scope"></a>Bewerkings bereik 

Het bewerkings bereik is geconfigureerd voor de geselecteerde bewerking.

1. Selecteer een **API**.
2. Selecteer de bewerking waarvoor u beleid wilt Toep assen.

    ![Bewerkings bereik](./media/api-management-howto-policies/operation-scope.png)

3. Klik op het pictogram drie hoek.
4. Selecteer **Code-editor**.
5. Beleids regels toevoegen of bewerken.
6. Klik op **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende Verwante onderwerpen:

+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voorbeelden van beleid](./policy-reference.md)