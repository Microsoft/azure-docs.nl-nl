---
title: Een aangepast CA-certificaat toevoegen - Azure API Management | Microsoft Docs
description: Meer informatie over het toevoegen van een aangepast CA-certificaat in Azure API Management. U kunt ook instructies zien voor het verwijderen van een certificaat.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51719f23302bfaa036f99e88fcd440d97ca3bc02
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817806"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Een aangepast CA-certificaat toevoegen in Azure API Management

Met Azure API Management u CA-certificaten installeren op de computer in de vertrouwde basis- en tussencertificaatopslag. Deze functionaliteit moet worden gebruikt als voor uw services een aangepast CA-certificaat is vereist.

In het artikel wordt beschreven hoe u CA-certificaten van een Azure API Management service-exemplaar in de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Een CA-certificaat uploaden

![CA-certificaten toevoegen](media/api-management-howto-ca-certificates/00.png)

Volg de onderstaande stappen om een nieuw CA-certificaat te uploaden. Als u nog geen service API Management service-exemplaar hebt gemaakt, bekijkt u de zelfstudie Een API Management [service-exemplaar maken.](get-started-create-service-instance.md)

1. Navigeer naar uw Azure API Management service-exemplaar in de Azure Portal.

2. Selecteer **CA-certificaten** in het menu.

3. Klik op de knop **+ Toevoegen**.  

    ![Schermopname van de knop + Toevoegen voor het toevoegen van een CA-certificaat.](media/api-management-howto-ca-certificates/01.png)  

4. Blader naar het certificaat en beslis over het certificaatopslag. Alleen de openbare sleutel is nodig, dus het wachtwoord is niet vereist.

    ![Schermopname die laat zien hoe u naar het certificaat bladert.](media/api-management-howto-ca-certificates/02.png)  

5. Klik op **Opslaan**. Deze bewerking kan enkele minuten duren.

    ![Schermopname die laat zien hoe u het certificaat op kunt slaan.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> U kunt een CA-certificaat uploaden met behulp van de `New-AzApiManagementSystemCertificate` PowerShell-opdracht.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Een clientcertificaat verwijderen

Als u een certificaat wilt verwijderen, klikt u op het contextmenu **...** en **selecteert u Verwijderen** naast het certificaat.

![CA-certificaten verwijderen](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
