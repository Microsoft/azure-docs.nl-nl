---
title: AWS CloudTrail verbinden met Azure Sentinel | Microsoft Docs
description: Gebruik de AWS-connector voor het delegeren van Azure Sentinel Access to AWS resource logs, het maken van een vertrouwens relatie tussen AWS CloudTrail en Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 3d24fa1ea046a860feb40d09a6d0a57c79371450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807594"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure Sentinel verbinden met AWS CloudTrail

Gebruik de AWS-connector om uw AWS CloudTrail Management-gebeurtenissen in azure Sentinel te streamen. Dit verbindings proces delegeert de toegang voor Azure Sentinel naar uw AWS-bron logboeken, waardoor er een vertrouwens relatie tussen AWS CloudTrail en Azure Sentinel wordt gemaakt. Dit wordt bereikt op AWS door een rol te maken waarmee u toegang krijgt tot de AWS-logboeken van Azure.

> [!NOTE]
> AWS CloudTrail heeft [ingebouwde beperkingen](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) in de LOOKUPEVENTS-API. Het kan Maxi maal twee trans acties per seconde (TPS) per account bevatten en elke query kan Maxi maal 50 records retour neren. Het gevolg hiervan is dat als één tenant constant meer dan 100 records per seconde genereert in één regio, dit leidt tot achterstand en vertragingen in de gegevensopname.

## <a name="prerequisites"></a>Vereisten

U moet schrijf machtiging hebben voor de Azure Sentinel-werk ruimte.

> [!NOTE]
> Azure Sentinel verzamelt CloudTrail-beheer gebeurtenissen uit alle regio's. Het is raadzaam om geen gebeurtenissen van de ene naar de andere regio te streamen.

## <a name="connect-aws"></a>Verbinding maken met AWS 


1. Selecteer in azure Sentinel **Data connectors** en selecteer vervolgens de regel **Amazon Web Services** in de tabel en klik in het deel venster AWS rechts op de **pagina connector openen**.

1. Volg de instructies onder **configuratie** met behulp van de volgende stappen.
 
1.  Selecteer in uw Amazon Web Services-console onder **beveiliging, identiteit & naleving** de optie **iam**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Kies **rollen** en selecteer **rol maken**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Kies **een ander AWS-account.** Voer in het veld **account-id** de **id van het micro soft-account** (**123412341234**) in dat u kunt vinden op de pagina AWS-connector in de Azure-Sentinel-Portal.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Zorg ervoor dat **externe ID vereisen** is geselecteerd en voer vervolgens de externe ID (werk ruimte-id) in die u kunt vinden op de pagina AWS-connector in de Azure-Sentinel-Portal.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Selecteer onder **machtigings beleid koppelen** de optie **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Voer een tag in (optioneel).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Voer vervolgens de naam van een **rol** in en selecteer de knop **rol maken** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Kies de rol die u hebt gemaakt in de lijst rollen.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Kopieer de **functie Arn**. Plak in de Azure Sentinel-Portal in het scherm Amazon Web Services-connector het in de **rol om een veld toe te voegen** en op **toevoegen** te klikken.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Als u het relevante schema in Log Analytics voor AWS gebeurtenissen wilt gebruiken, zoekt u naar **AWSCloudTrail**.

    > [!IMPORTANT]
    > Vanaf 1 december 2020 is het veld **AwsRequestId** vervangen door het veld **AwsRequestId_** (het toegevoegde onderstrepings teken). De gegevens in het veld Old **AwsRequestId** blijven behouden aan het einde van de opgegeven Bewaar periode van de klant.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u AWS CloudTrail verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
