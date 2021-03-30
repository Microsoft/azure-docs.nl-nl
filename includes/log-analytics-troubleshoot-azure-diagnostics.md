---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176428"
---
### <a name="troubleshoot-azure-diagnostics"></a>Problemen oplossen met Azure Diagnostics

Als u het volgende foutbericht ontvangt, is de resourceprovider Microsoft.insights niet geregistreerd:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Voer de volgende stappen in Azure Portal uit om de resourceprovider te registreren:

1.  Klik in het navigatiedeelvenster aan de linkerkant op *Abonnementen*
2.  Selecteer het abonnement dat wordt vermeld in het foutbericht
3.  Klik op *Resourceproviders*
4.  Ga naar de *Microsoft.insights*-provider
5.  Klik op de koppeling *Registreren*

![Registreer de resourceprovider Microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Nadat de *Microsoft.insights* resourceprovider is geregistreerd, configureert u Diagnostics opnieuw.


Als u het volgende foutbericht ontvangt in PowerShell, moet u uw versie van PowerShell bijwerken:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Werk uw versie van Azure PowerShell bij en volg de instructies in het artikel [Azure PowerShell installeren](/powershell/azure/install-az-ps).
