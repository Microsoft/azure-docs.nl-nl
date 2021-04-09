---
title: Fout opsporing op SAML gebaseerde eenmalige aanmelding Azure Active Directory
description: Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory.
services: active-directory
ms.author: kenwith
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: 4a5fbef3380db8c9b357c83b4cb8240a41a0012c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259284"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Foutopsporing uitvoeren in op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory

Meer informatie over het zoeken en oplossen van problemen met eenmalige [aanmelding](what-is-single-sign-on.md) voor toepassingen in azure Active Directory (Azure AD) die gebruikmaken van eenmalige aanmelding op basis van SAML. 

## <a name="before-you-begin"></a>Voordat u begint

U wordt aangeraden de [beveiligde aanmeldings extensie voor mijn apps](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)te installeren. Met deze browser extensie kunt u eenvoudig de SAML-aanvraag en informatie over SAML-antwoorden verzamelen die u nodig hebt om problemen met eenmalige aanmelding op te lossen. Als u de uitbrei ding niet kunt installeren, wordt in dit artikel beschreven hoe u problemen kunt oplossen met en zonder dat de extensie is geïnstalleerd.

Gebruik een van de volgende koppelingen om de beveiligde aanmeldings extensie voor mijn apps te downloaden en te installeren.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Op SAML gebaseerde eenmalige aanmelding testen

Op SAML gebaseerde eenmalige aanmelding op basis van Azure AD en een doel toepassing testen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder of een andere beheerder die gemachtigd is om toepassingen te beheren.
1. Selecteer in de Blade links **Azure Active Directory** en selecteer vervolgens **bedrijfs toepassingen**. 
1. Selecteer in de lijst met bedrijfs toepassingen de toepassing waarvoor u eenmalige aanmelding wilt testen en klik vervolgens in de opties aan de linkerkant op **eenmalige aanmelding** selecteren.
1. Als u de test ervaring voor eenmalige aanmelding op basis van SAML wilt openen, gaat u naar **test eenmalige aanmelding** (stap 5). Als de knop **testen** grijs wordt weer gegeven, moet u de vereiste kenmerken eerst invullen en opslaan in het gedeelte **basis configuratie van SAML** .
1. Gebruik op de Blade **eenmalige aanmelding testen** de bedrijfs referenties om u aan te melden bij de doel toepassing. U kunt zich aanmelden als de huidige gebruiker of als een andere gebruiker. Als u zich als een andere gebruiker aanmeldt, wordt u gevraagd om u te verifiëren.

    ![Scherm opname van de pagina SAML SSO testen](./media/debug-saml-sso-issues/test-single-sign-on.png)

Als u bent aangemeld, is de test voltooid. In dit geval heeft Azure AD een SAML-antwoord token uitgegeven aan de toepassing. De toepassing heeft het SAML-token gebruikt om u aan te melden.

Als er een fout optreedt op de aanmeldings pagina van het bedrijf of de pagina van de toepassing, gebruikt u een van de volgende secties om de fout op te lossen.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Een aanmeldings fout op de aanmeldings pagina van uw bedrijf oplossen

Wanneer u zich probeert aan te melden, wordt mogelijk een fout weer gegeven op de aanmeldings pagina van uw bedrijf, zoals in het volgende voor beeld.

![Voor beeld van een fout op de aanmeldings pagina van het bedrijf](./media/debug-saml-sso-issues/error.png)

Als u deze fout wilt opsporen, hebt u het fout bericht en de SAML-aanvraag nodig. Met de beveiligde aanmeldings extensie van mijn apps wordt deze informatie automatisch verzameld en worden richt lijnen voor oplossingen weer gegeven in azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>De aanmeldings fout oplossen met de beveiligde aanmeldings extensie van mijn apps geïnstalleerd

1. Als er een fout optreedt, wordt u teruggeleid naar de Blade **eenmalige aanmelding van Azure AD-test** .
1. Selecteer op de Blade **eenmalige aanmelding testen** **de optie de SAML-aanvraag downloaden**.
1. Er moeten specifieke richt lijnen voor oplossingen worden weer gegeven op basis van de fout en de waarden in de SAML-aanvraag.
1. Er wordt een **Fix it** -knop weer geven om de configuratie in azure AD automatisch bij te werken om het probleem op te lossen. Als u deze knop niet ziet, wordt het aanmeldings probleem niet veroorzaakt door een onjuiste configuratie in azure AD.

Als er geen oplossing wordt aangeboden voor de aanmeldings fout, wordt u aangeraden het tekstvak feedback te gebruiken om ons op de hoogte te stellen.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Om de fout op te lossen zonder de beveiligde aanmeldings extensie mijn apps te installeren

1. Kopieer het fout bericht in de rechter benedenhoek van de pagina. Het fout bericht bevat:
    - Een CorrelationID en tijds tempel. Deze waarden zijn belang rijk wanneer u een ondersteunings aanvraag maakt met micro soft omdat ze de technici helpen bij het identificeren van uw probleem en een nauw keurige oplossing bieden voor uw probleem.
    - Een instructie voor het identificeren van de hoofd oorzaak van het probleem.
1. Ga terug naar Azure AD en zoek de Blade **eenmalige aanmelding testen** .
1. Plak in het bovenstaande tekstvak de **richt lijnen voor probleem oplossing** de fout melding.
1. Klik op **oplossings richtlijnen ophalen** om de stappen voor het oplossen van het probleem weer te geven. Voor de richt lijnen is mogelijk informatie vereist van de SAML-aanvraag of het SAML-antwoord. Als u de beveiligde aanmeldings extensie voor mijn apps niet gebruikt, hebt u mogelijk een hulp programma zoals [Fiddler](https://www.telerik.com/fiddler) nodig om de SAML-aanvraag en het-antwoord op te halen.
1. Controleer of de bestemming in de SAML-aanvraag overeenkomt met de URL van de SAML single Sign-On-service die is verkregen van Azure AD.
1. Controleer of de uitgever in de SAML-aanvraag dezelfde id is die u hebt geconfigureerd voor de toepassing in azure AD. Azure AD maakt gebruik van de verlener om een toepassing in uw directory te vinden.
1. Controleer of AssertionConsumerServiceURL de toepassing verwacht het SAML-token van Azure AD te ontvangen. U kunt deze waarde configureren in azure AD, maar dit is niet verplicht als deze deel uitmaakt van de SAML-aanvraag.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Een aanmeldings fout op de toepassings pagina oplossen

U kunt zich aanmelden en vervolgens een fout zien op de pagina van de toepassing. Dit doet zich voor wanneer Azure AD een token aan de toepassing heeft uitgegeven, maar de aanvraag niet wordt geaccepteerd.

Volg deze stappen om de fout op te lossen of Bekijk deze [korte video over het gebruik van Azure AD voor het oplossen van problemen met SAML SSO](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8):

1. Als de toepassing zich in de Azure AD-galerie bevindt, controleert u of u alle stappen hebt gevolgd voor het integreren van de toepassing met Azure AD. Zie de [lijst met zelf studies voor SaaS-toepassingen](../saas-apps/tutorial-list.md)voor informatie over de integratie-instructies voor uw toepassing.
1. Het SAML-antwoord ophalen.
    - Als de beveiligde aanmeldings extensie voor mijn apps is geïnstalleerd, klikt u op de Blade **eenmalige aanmelding testen** op **het SAML-antwoord downloaden**.
    - Als de extensie niet is geïnstalleerd, gebruikt u een hulp programma zoals [Fiddler](https://www.telerik.com/fiddler) om het SAML-antwoord op te halen.
1. U ziet deze elementen in het SAML-respons token:
   - Unieke id van de gebruiker van de NameID-waarde en-indeling
   - Claims die zijn uitgegeven in het token
   - Certificaat dat wordt gebruikt om het token te ondertekenen.

     Zie het [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over het SAML-antwoord.

1. Nu u het SAML-antwoord hebt gecontroleerd, raadpleegt u de [fout op de pagina van een toepassing nadat](application-sign-in-problem-application-error.md) u zich hebt aangemeld voor hulp bij het oplossen van het probleem. 
1. Als u zich nog steeds niet kunt aanmelden, kunt u de leverancier van de toepassing vragen wat er ontbreekt in het SAML-antwoord.

## <a name="next-steps"></a>Volgende stappen

Nu eenmalige aanmelding aan uw toepassing werkt, kunt u het inrichten van [gebruikers en](../app-provisioning/user-provisioning.md) het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren of aan de [slag met voorwaardelijke toegang](../conditional-access/app-based-conditional-access.md).
