---
title: Hash-algoritme voor hand tekening wijzigen voor Microsoft 365 Relying Party Trust-Azure
description: Deze pagina bevat richt lijnen voor het wijzigen van het SHA-algoritme voor federatieve vertrouwens relaties met Microsoft 365.
keywords: SHA1, SHA256, M365, Federatie, aadconnect, ADFS, AD FS, SHA wijzigen, federatieve vertrouwens relatie Relying Party vertrouwen
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660923"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Hash-algoritme voor hand tekening voor Microsoft 365 Relying Party vertrouwen wijzigen
## <a name="overview"></a>Overzicht
Met Active Directory Federation Services (AD FS) worden de tokens van Microsoft Azure Active Directory ondertekend om te controleren of er niet kan worden geknoeid. Deze hand tekening kan worden gebaseerd op SHA1 of SHA256. Azure Active Directory ondersteunt nu tokens die zijn ondertekend met een SHA256-algoritme, en wij raden u aan het algoritme voor token-ondertekening in te stellen op SHA256 voor het hoogste beveiligings niveau. In dit artikel worden de stappen beschreven die nodig zijn om het algoritme voor token-ondertekening in te stellen op het niveau van veiligere SHA256.

>[!NOTE]
>Micro soft adviseert het gebruik van SHA256 als algoritme voor het ondertekenen van tokens, omdat het veiliger is dan SHA1, maar SHA1 nog steeds een ondersteunde optie blijft.

## <a name="change-the-token-signing-algorithm"></a>Het algoritme voor token-ondertekening wijzigen
Nadat u de handtekening algoritme hebt ingesteld met een van de twee onderstaande processen, worden de tokens in AD FS ondertekend voor Microsoft 365 Relying Party vertrouwens relatie met SHA256. U hoeft geen extra configuratie wijzigingen aan te brengen en deze wijziging heeft geen invloed op de mogelijkheid om toegang te krijgen tot Microsoft 365 of andere Azure AD-toepassingen.

### <a name="ad-fs-management-console"></a>AD FS-beheer console
1. Open de AD FS-beheer console op de primaire AD FS-server.
2. Vouw het knoop punt AD FS uit en klik op **vertrouwens relaties met relying party's**.
3. Klik met de rechter muisknop op de vertrouwens relatie van Microsoft 365/Azure Relying Party en selecteer **Eigenschappen**.
4. Selecteer het tabblad **Geavanceerd** en selecteer de Secure Hash Algorithm sha256.
5. Klik op **OK**.

![SHA256-handtekening algoritme--MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS Power shell-cmdlets
1. Open Power shell onder Administrator bevoegdheden op een AD FS server.
2. Stel het algoritme voor beveiligde hashes in met behulp van de cmdlet **set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Ook lezen
* [Microsoft 365 vertrouwen met Azure AD Connect herstellen](how-to-connect-fed-management.md#repairthetrust)

