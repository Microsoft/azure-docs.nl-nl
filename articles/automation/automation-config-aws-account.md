---
title: Azure Automation-runbooks verifiëren met Amazon Web Services
description: In dit artikel wordt uitgelegd hoe u runbooks verifieert met Amazon Web Services.
keywords: AWS-verificatie, AWS configureren
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83837180"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Runbooks verifiëren met Amazon Web Services

Het automatiseren van algemene taken met resources in Amazon Web Services (AWS) kan worden bewerkstelligd met Automation-runbooks in Azure. U kunt vele taken in AWS automatiseren met Automation-runbooks, net zoals u dit kunt met resources in Azure. Voor verificatie moet u een Azure-abonnement hebben.

## <a name="obtain-aws-subscription-and-credentials"></a>AWS-abonnement en-referenties verkrijgen

Als u wilt verifiëren met AWS, moet u een AWS-abonnement verkrijgen en een set AWS-referenties opgeven om uw runbooks te verifiëren die vanuit Azure Automation worden uitgevoerd. Specifieke referenties zijn vereist, de AWS-toegangs sleutel en de geheime sleutel. Zie [AWS-referenties gebruiken](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Automation-account configureren

U kunt een bestaand Automation-account gebruiken om te verifiëren met AWS. U kunt ook een account toewijzen voor runbooks die gericht zijn op AWS resources. In dit geval maakt u een nieuw [Automation-account](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>AWS-referenties opslaan

U moet de AWS-referenties opslaan als assets in Azure Automation. Zie [toegangs sleutels voor uw AWS-account beheren](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) voor instructies over het maken van de toegangs sleutel en de geheime sleutel. Wanneer de sleutels beschikbaar zijn, kopieert u de toegangs sleutel-ID en de geheime sleutel-ID op een veilige plaats. U kunt uw sleutel bestand downloaden om het op een veilige plek op te slaan.

## <a name="create-credential-asset"></a>Referentie-Asset maken

Nadat u uw AWS-beveiligings sleutels hebt gemaakt en gekopieerd, moet u een referentie-element maken met het Automation-account. Met de Asset kunt u de AWS-sleutels veilig opslaan en ernaar verwijzen in uw runbooks. Zie [een nieuw referentie-element maken met de Azure Portal](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Voer de volgende AWS-informatie in de opgegeven velden in:
    
* **Naam**  -  **AWScred** of een geschikte waarde volgen volgens uw naamgevings standaarden
* **Gebruikers naam** -uw toegangs-id
* **Wacht woord** : naam van de geheime sleutel 

## <a name="next-steps"></a>Volgende stappen

* Zie [een Amazon Web Services VM implementeren met een runbook](automation-scenario-aws-deployment.md)voor meer informatie over het maken van runbooks om taken te AUTOMATISEREN in AWS.