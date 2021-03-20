---
title: Een aangepast CA-certificaat toevoegen-Azure API Management | Microsoft Docs
description: Meer informatie over het toevoegen van een aangepast CA-certificaat in azure API Management. U kunt ook instructies voor het verwijderen van een certificaat weer geven.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102034"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Een aangepast CA-certificaat toevoegen in azure API Management

Met Azure API Management kunnen CA-certificaten op de computer worden geïnstalleerd binnen de vertrouwde basis-en tussenliggende certificaat archieven. Deze functie moet worden gebruikt als voor uw services een aangepast CA-certificaat is vereist.

In dit artikel wordt beschreven hoe u CA-certificaten van een exemplaar van Azure API Management service beheert in de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Een CA-certificaat uploaden

![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/00.png)

Volg de onderstaande stappen om een nieuw CA-certificaat te uploaden. Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u de zelf studie [een API Management service-exemplaar maken](get-started-create-service-instance.md).

1. Navigeer naar uw Azure API Management service-exemplaar in de Azure Portal.

2. Selecteer **CA-certificaten** in het menu.

3. Klik op de knop **+ Toevoegen**.  

    ![Scherm opname van de knop + toevoegen voor het toevoegen van een CA-certificaat.](media/api-management-howto-ca-certificates/01.png)  

4. Blader naar het certificaat en beslis het certificaat archief. Alleen de open bare sleutel is nodig, dus het wacht woord is niet vereist.

    ![Scherm afbeelding die laat zien hoe u naar het certificaat bladert.](media/api-management-howto-ca-certificates/02.png)  

5. Klik op **Opslaan**. Deze bewerking kan enkele minuten duren.

    ![Scherm afbeelding die laat zien hoe het certificaat moet worden opgeslagen.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> U kunt een CA-certificaat uploaden met behulp van de `New-AzApiManagementSystemCertificate` Power shell-opdracht.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Een client certificaat verwijderen

Als u een certificaat wilt verwijderen, klikt u op context menu **...** en selecteert u **verwijderen** naast het certificaat.

![CA-certificaten verwijderen](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
