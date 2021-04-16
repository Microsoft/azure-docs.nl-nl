---
title: 'Snelstart: Rekenkracht opschalen voor Synapse SQL-pool (Azure Portal)'
description: U kunt rekenkracht opschalen voor Synapse SQL-pool (datawarehouse) met behulp van de Azure Portal.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534222"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snelstart: Rekenkracht opschalen voor Synapse SQL-pool met de Azure Portal

U kunt rekenkracht opschalen voor Synapse SQL-pool (datawarehouse) met behulp van de Azure Portal. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen. 

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Voordat u begint

U kunt een eigen SQL-pool schalen of u kunt [Quickstart: maken en verbinden - portal](create-data-warehouse-portal.md) gebruiken om een SQL-pool met de naam **mySampleDataWarehouse** te maken. Met deze snelstart wordt **mySampleDataWarehouse** geschaald.

>[!IMPORTANT] 
>Uw SQL-pool moet online zijn om te kunnen schalen. 

## <a name="scale-compute"></a>De schaal van Compute aanpassen

Berekeningsresources van SQL-pool kunnen worden geschaald door het aantal warehouse-eenheden te vergroten of verkleinen. In de [Quickstart: maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU’s voor **mySampleDataWarehouse** aangepast.

DWU’s wijzigen:

1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure Portal.
2. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De SQL-pool wordt geopend.
3. Klik op **Schalen**.

    ![Op Schalen klikken](./media/quickstart-scale-compute-portal/click-scale.png)

2. Verplaats in het paneel Schalen de schuifregelaar naar links of rechts om de DWU-instelling te wijzigen. Selecteer vervolgens schalen.

    ![Schuifregelaar verplaatsen](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over SQL-pool naar de zelfstudie [Gegevens in een SQL-pool laden](./load-data-from-azure-blob-storage-using-copy.md).
