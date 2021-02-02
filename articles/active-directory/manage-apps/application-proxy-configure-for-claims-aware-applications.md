---
title: Claim bewuste apps-Azure AD-app proxy | Microsoft Docs
description: On-premises ASP.NET-toepassingen publiceren die ADFS-claims voor veilige externe toegang door uw gebruikers accepteren.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c2d47e70a4f7595ac535d5f8ba9506087eb72
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258521"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Werken met claim bewuste apps in toepassings proxy
[Claim bewuste apps](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) voeren een omleiding naar de Security Token Service (STS). De STS vraagt referenties van de gebruiker in ruil voor een token en leidt de gebruiker vervolgens naar de toepassing. Er zijn enkele manieren om de toepassings proxy in te scha kelen voor gebruik van deze omleidingen. Gebruik dit artikel om uw implementatie te configureren voor claim bewuste apps. 

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat de STS waarmee de claim bewuste app omleidt, beschikbaar is buiten uw on-premises netwerk. U kunt de STS beschikbaar maken door deze via een proxy weer te geven of door externe verbindingen toe te staan. 

## <a name="publish-your-application"></a>Uw toepassing publiceren

1. Publiceer uw toepassing volgens de instructies in [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md).
2. Ga naar de toepassings pagina in de portal en selecteer **eenmalige aanmelding**.
3. Als u **Azure Active Directory** hebt gekozen als uw **methode voor verificatie** vooraf, selecteert u **eenmalige aanmelding voor Azure AD is uitgeschakeld** als uw **interne verificatie methode**. Als u **passthrough** hebt gekozen als uw **methode voor verificatie vooraf**, hoeft u niets te wijzigen.

## <a name="configure-adfs"></a>ADFS configureren

U kunt AD FS op een van de volgende twee manieren configureren voor claim bewuste apps. De eerste is met behulp van aangepaste domeinen. De tweede is met WS-Federation. 

### <a name="option-1-custom-domains"></a>Optie 1: aangepaste domeinen

Als alle interne Url's voor uw toepassingen FQDN-namen (FULLy Qualified Domain names) zijn, kunt u [aangepaste domeinen](application-proxy-configure-custom-domain.md) voor uw toepassingen configureren. Gebruik de aangepaste domeinen om externe Url's te maken die gelijk zijn aan de interne Url's. Wanneer uw externe Url's overeenkomen met uw interne Url's, werken de STS-omleidingen, ongeacht of uw gebruikers on-premises of extern zijn. 

### <a name="option-2-ws-federation"></a>Optie 2: WS-Federation

1. Open ADFS-beheer.
2. Ga naar **Relying Party vertrouwens relaties**, klik met de rechter muisknop op de app die u wilt publiceren met toepassings proxy en kies **Eigenschappen**.  

   ![Vertrouwens relaties Relying Party Klik met de rechter muisknop op app-naam-scherm afbeelding](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Selecteer op het tabblad **eind punten** onder **type eind punt** de optie **WS-Federation**.
4. Onder **vertrouwde URL** voert u de URL in die u hebt ingevoerd in de toepassings proxy onder **externe URL** en klikt u op **OK**.  

   ![Een eind punt met een vertrouwde URL-set toevoegen](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Volgende stappen
* [Systeem eigen client-apps inschakelen voor interactie met proxy toepassingen](application-proxy-configure-native-client-application.md)