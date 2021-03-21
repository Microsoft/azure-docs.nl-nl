---
title: Verbinding maken met Azure Analysis Services met Excel | Microsoft Docs
description: Meer informatie over het maken van verbinding met een Azure Analysis Services-server met behulp van Excel. Nadat de verbinding tot stand is gebracht, kunnen gebruikers draai tabellen maken om gegevens te verkennen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fccb919a7eac79080fefeac70326b8aa7a967622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499467"
---
# <a name="connect-with-excel"></a>Verbinden met Excel

Wanneer u een server hebt gemaakt en er een tabellair model op hebt geïmplementeerd, kunnen clients verbinding maken en beginnen met het verkennen van gegevens. 

## <a name="before-you-begin"></a>Voordat u begint

Het account waarmee u zich aanmeldt, moet deel uitmaken van een model database functie met ten minste lees machtigingen. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Verbinding maken in Excel

Het maken van verbinding met een server in Excel wordt ondersteund met behulp van gegevens ophalen in Excel 2016 en hoger. Het maken van verbinding met behulp van de wizard tabel importeren in Power Pivot wordt niet ondersteund. 

1. Klik in Excel op het lint **gegevens** op **gegevens ophalen**  >  **uit data base**  >  **van Analysis Services**.

2. Voer in de wizard gegevens verbinding, in **Server naam**, de server naam in, inclusief protocol en URI. Bijvoorbeeld asazure://westcentralus.asazure.windows.net/advworks. Selecteer vervolgens bij **aanmeldings referenties** **de volgende gebruikers naam en wacht woord gebruiken**, en typ vervolgens de gebruikers naam van de organisatie, bijvoorbeeld nancy@adventureworks.com en wacht woord.

    > [!IMPORTANT]
    > Als u zich aanmeldt met een micro soft-account, Live ID, Yahoo, Gmail enzovoort of u verplicht zich aan te melden met multi-factor Authentication, laat u het veld wacht woord leeg. U wordt gevraagd om een wacht woord nadat u op volgende hebt geklikt. 

    ![Verbinding maken vanuit Excel-aanmelding](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Selecteer in **Data Base en tabel selecteren** de data base en het model of perspectief en klik vervolgens op **volt ooien**.
   
    ![Verbinding maken vanuit Excel model selecteren](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zie ook

[Client bibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)   
[Uw server beheren](analysis-services-manage.md)
