---
title: Een Azure-bestands share koppelen aan een aan een AD DS gekoppelde VM
description: Meer informatie over het koppelen van een bestands share aan uw on-premises Active Directory Domain Services-computers.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 1dd2768c7541312b68e2a44a35fc4260d67c5655
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586995"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Deel vier: een bestands share koppelen vanaf een virtuele machine die lid is van een domein

Voordat u aan dit artikel begint, moet u ervoor zorgen dat u het vorige artikel hebt voltooid, [machtigingen voor mappen en bestands niveau via SMB configureert](storage-files-identity-ad-ds-configure-permissions.md).

Het proces dat in dit artikel wordt beschreven, controleert of uw bestands share en toegangs machtigingen juist zijn ingesteld en dat u toegang hebt tot een Azure-bestands share vanaf een virtuele machine die lid is van een domein. De toewijzing van Azure-rollen op share niveau kan enige tijd in beslag nemen. 

Meld u aan bij de client met behulp van de referenties waaraan u machtigingen hebt verleend, zoals wordt weer gegeven in de volgende afbeelding.

![Scherm afbeelding van het aanmeldings scherm van Azure AD voor gebruikers verificatie](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Vereisten koppelen

Voordat u de bestands share kunt koppelen, moet u ervoor zorgen dat u de volgende vereisten hebt door lopen:

- Als u de bestands share koppelt van een client die eerder de bestands share heeft gekoppeld met behulp van de sleutel van uw opslag account, moet u ervoor zorgen dat u de verbinding met de share hebt verbroken, de permanente referenties van de sleutel van het opslag account hebt verwijderd en momenteel AD DS referenties gebruikt voor verificatie. Raadpleeg de [pagina Veelgestelde vragen](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication)voor instructies voor het wissen van de gekoppelde share met de sleutel van het opslag account.
- Uw-client moet een gezichts lijn hebben voor uw AD DS. Als uw computer of virtuele machine zich buiten het netwerk bevindt dat wordt beheerd door uw AD DS, moet u VPN inschakelen om AD DS voor verificatie te bereiken.

Vervang de waarden van de tijdelijke aanduiding door uw eigen waarden en gebruik vervolgens de volgende opdracht om de Azure-bestands share te koppelen. U moet altijd koppelen met behulp van het hieronder weer gegeven pad. Het gebruik van CNAME voor bestands koppeling wordt niet ondersteund voor verificatie op basis van identiteiten (AD DS of Azure AD DS).

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Als u problemen ondervindt met het koppelen van AD DS referenties, raadpleegt u [Azure files niet koppelen aan AD-referenties](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) voor hulp.

Als het koppelen van uw bestands share is geslaagd, hebt u de on-premises AD DS verificatie voor uw Azure-bestands shares ingeschakeld en geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Als de identiteit die u hebt gemaakt in AD DS voor de weer gave van het opslag account zich in een domein of OE bevindt waarin het roteren van wacht woorden afdwingt, gaat u door naar het volgende artikel voor instructies voor het bijwerken van uw wacht woord:

[Het wacht woord van de identiteit van uw opslag account bijwerken in AD DS](storage-files-identity-ad-ds-update-password.md)