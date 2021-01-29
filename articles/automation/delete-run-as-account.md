---
title: Een Azure Automation uitvoeren als-account verwijderen
description: In dit artikel leest u hoe u een uitvoeren als-account verwijdert met Power shell of via de Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055750"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account verwijderen

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources op het Azure Resource Manager of het klassieke Azure-implementatie model met behulp van Automation-runbooks en andere automatiserings functies. In dit artikel wordt beschreven hoe u een uitvoeren als-of klassiek uitvoeren als-account verwijdert. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het uitvoeren als-account hebt verwijderd, kunt u het opnieuw maken in de Azure Portal of met het Power shell-script.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen

1. Open in Azure Portal het Automation-account.

2. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.

3. Selecteer op de pagina Uitvoeren als-accounts het Uitvoeren als- of klassieke Uitvoeren als-account dat u wilt verwijderen.

4. Klik in het deel venster Eigenschappen voor het geselecteerde account op **verwijderen**.

   ![Uitvoeren als-account verwijderen](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="next-steps"></a>Volgende stappen

Zie [Run as-accounts maken](create-run-as-account.md)als u het uitvoeren als-of klassiek uitvoeren als-account opnieuw wilt maken.