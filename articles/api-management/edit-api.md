---
title: Een API bewerken met Azure Portal | Microsoft Docs
description: Leer hoe u API Management (APIM) gebruikt om een API te bewerken. U kunt bewerkingen in het APIM-exemplaar toevoegen, verwijderen of hernoemen, of de swagger van de API bewerken.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 1c4e64251390936e8a63ee904ec69f173cac6114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146702"
---
# <a name="edit-an-api"></a>Een API bewerken

Deze zelfstudie laat u zien hoe u een API Management (APIM) moet gebruiken om een API te bewerken. 

+ U kunt dit doen door het toevoegen, verwijderen, wijzigen van bewerkingen in het APIM-exemplaar. 
+ U kunt de swagger van uw API bewerken.

## <a name="prerequisites"></a>Vereisten

+ [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ [Uw eerste API importeren en publiceren](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Een API in APIM bewerken

![Schermopname met markeringen van het proces voor het bewerken van een API in APIM.](./media/edit-api/edit-api001.png)

1. Klik op het tabblad **API's**.
2. Selecteer een van de API's die u eerder hebt geïmporteerd.
3. Selecteer het tabblad **Ontwerpen**.
4. Selecteer een bewerking die u wilt bewerken.
5. Om de naam van de bewerking te wijzigen, selecteert u een **potlood** in het **Front-end**-venster.

## <a name="update-the-swagger"></a>Werk de swagger bij

U kunt uw back-end-API van Azure Portal bijwerken met de volgende stappen:

1. Selecteer **Alle bewerkingen**
2. Klik op het potlood in het **Front-end**-venster.

    ![Schermopname met het potloodpictogram in het Frontend-scherm gemarkeerd.](./media/edit-api/edit-api002.png)

    Uw API swagger wordt weergegeven.

    ![Een API bewerken](./media/edit-api/edit-api003.png)

3. Werk de swagger bij.
4. Klik op **Opslaan**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeelden van APIM beleid](./policy-reference.md)
> [Een gepubliceerde API beveiligen transformeren en beveiligen](transform-api.md)