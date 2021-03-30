---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "75659866"
---
>[!IMPORTANT]
>U kunt de resources die u hebt gemaakt, gebruiken als vereisten voor andere Azure Machine Learning-zelfstudies en artikelen met procedures.

### <a name="delete-everything"></a>Alles verwijderen

Als u niets wilt gebruiken dat u hebt gemaakt, kunt u de hele resourcegroep verwijderen zodat er geen kosten voor in rekening worden gebracht.

1. Selecteer in Azure Portal, aan de linkerkant in het venster, de optie **Resourcegroepen**.
 
   ![Een resourcegroep verwijderen in de Azure-portal](./media/aml-ui-cleanup/delete-resources.png)

1. Selecteer de resourcegroep die u hebt gemaakt in de lijst.

1. Selecteer **Resourcegroep verwijderen**.

Als u de resource groep verwijdert, worden ook alle resources verwijderd die u in de ontwerpfunctie hebt gemaakt. 

### <a name="delete-individual-assets"></a>Afzonderlijke assets verwijderen

In de ontwerpfunctie waar u uw experiment hebt gemaakt, verwijdert u afzonderlijke assets door ze te selecteren en vervolgens de knop **Verwijderen** te selecteren.

Het rekendoel dat u hier hebt gemaakt, wordt, wanneer het niet wordt gebruikt, *automatisch geschaald* naar nul knooppunten. Deze actie wordt uitgevoerd om de kosten te minimaliseren.  Als u het rekendoel wilt verwijderen, voert u de volgende stappen uit:

![Assets verwijderen](./media/aml-ui-cleanup/delete-asset.png)

U kunt de registratie van gegevenssets vanuit uw werkruimte opheffen door alle gegevenssets te selecteren en **Registratie opheffen** te selecteren.

![Registratie van gegevensset ongedaan maken](./media/aml-ui-cleanup/unregister-dataset1225.png)

Als u een gegevensset wilt verwijderen, gaat u naar het opslagaccount via Azure Portal of Azure Storage Explorer en verwijdert u de assets handmatig.


