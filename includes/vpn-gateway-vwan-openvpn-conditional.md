---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471507"
---
Met voorwaardelijke toegang kunt u nauw keurig toegangs beheer per toepassing instellen. Als u voorwaardelijke toegang wilt gebruiken, moet u Azure AD Premium 1 of hoger licentiëring hebben toegepast op de gebruikers die zijn onderworpen aan de regels voor voorwaardelijke toegang.

1. Ga naar de pagina **Enter prise Applications-alle toepassingen** en klik op **Azure VPN**.

   - Klik op **voorwaardelijke toegang**.
   - Klik op **Nieuw beleid** om het **nieuwe** deel venster te openen.
2. Navigeer in het **nieuwe** deel venster naar **toewijzingen-> gebruikers en groepen**. Op het tabblad **gebruikers en groepen->** **toevoegen** :

   - Klik op **gebruikers en groepen selecteren**.
   - Controleer de **gebruikers en groepen**.
   - Klik op **selecteren** om een groep of een set gebruikers te selecteren die wordt beïnvloed door MFA.
   - Klik op **Gereed**.

   ![Toewijzingen](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Ga in het **nieuwe** deel venster naar het deel venster **toegangs beheer-> toekenning** :

   - Klik op **toegang verlenen**.
   - Klik op **multi-factor Authentication vereisen**.
   - Klik op **alle geselecteerde besturings elementen vereisen**.
   - Klik op **Selecteren**.
   
   ![Toegang verlenen voor MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. In de sectie **beleid inschakelen** :

   - Selecteer **aan**.
   - Klik op **Create**.

   ![Beleid inschakelen](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)