---
title: 'Snelstart: Rekenkracht opschalen voor Synapse SQL-pool (Azure Portal)'
description: U kunt rekenkracht opschalen voor Synapse SQL-pool (datawarehouse) met behulp van de Azure Portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 26a8a865a787a9c9b17031f94456272c93380704
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117041"
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