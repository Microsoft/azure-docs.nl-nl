---
title: Fouten opsporen in een aanmelding op basis van SAML - Azure Active Directory
description: Fouten opsporen in op SAML gebaseerde een aanmelding bij toepassingen in Azure Active Directory.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374315"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Foutopsporing uitvoeren in op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory

Informatie over het [](what-is-single-sign-on.md) vinden en oplossen van problemen met een enkele aanmelding voor toepassingen in Azure Active Directory (Azure AD) die gebruikmaken van een aanmelding op basis van SAML. 

## <a name="before-you-begin"></a>Voordat u begint

U wordt aangeraden de [Mijn apps-extensie voor beveiligde aanmelding te installeren.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) Met deze browserextensie kunt u eenvoudig de SAML-aanvraag- en SAML-antwoordinformatie verzamelen die u nodig hebt om problemen met een aanmelding op te lossen. Als u de extensie niet kunt installeren, wordt in dit artikel beschreven hoe u problemen met en zonder de geïnstalleerde extensie kunt oplossen.

Gebruik een van de volgende koppelingen om Mijn apps beveiligde aanmeldingsextensie te downloaden en te installeren.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Een aanmelding op basis van SAML testen

Een aanmelding op basis van SAML testen tussen Azure AD en een doeltoepassing:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder of andere beheerder die gemachtigd is om toepassingen te beheren.
1. Selecteer in de linkerblade **Azure Active Directory** en selecteer vervolgens **Bedrijfstoepassingen.** 
1. Selecteer in de lijst met bedrijfstoepassingen de toepassing waarvoor u een aanmelding wilt testen en selecteer vervolgens in de opties aan de linkerkant **Een aanmelding.**
1. Ga naar Test **single sign-on** (stap 5) om de op SAML gebaseerde testervaring voor een aanmelding te openen. Als de **knop Testen** grijs wordt weergeven, moet u eerst de vereiste kenmerken invullen en opslaan in de sectie **Standaard SAML-configuratie.**
1. Gebruik op **de blade Test-een-op-een-aanmelding** uw bedrijfsreferenties om u aan te melden bij de doeltoepassing. U kunt zich aanmelden als de huidige gebruiker of als een andere gebruiker. Als u zich als een andere gebruiker aanmelden, wordt u gevraagd om te verifiëren.

    ![Schermopname van de saml-pagina voor eenmalige aanmelding testen](./media/debug-saml-sso-issues/test-single-sign-on.png)

Als u bent aangemeld, is de test geslaagd. In dit geval heeft Azure AD een SAML-antwoord token uitgegeven aan de toepassing. De toepassing heeft het SAML-token gebruikt om u aan te melden.

Als er een fout is opgetreden op de aanmeldingspagina van het bedrijf of op de pagina van de toepassing, gebruikt u een van de volgende secties om de fout op te lossen.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Een aanmeldingsfout op de aanmeldingspagina van uw bedrijf oplossen

Wanneer u zich probeert aan te melden, ziet u mogelijk een foutmelding op de aanmeldingspagina van uw bedrijf die vergelijkbaar is met het volgende voorbeeld.

![Voorbeeld van een fout op de aanmeldingspagina van het bedrijf](./media/debug-saml-sso-issues/error.png)

Als u deze fout wilt opsporen, hebt u het foutbericht en de SAML-aanvraag nodig. De Mijn apps beveiligde aanmeldingsextensie verzamelt deze informatie automatisch en geeft richtlijnen voor oplossing weer in Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Om de aanmeldingsfout op te lossen met Mijn apps beveiligde aanmeldingsextensie geïnstalleerd

1. Wanneer er een fout optreedt, wordt u door de extensie teruggeleid naar de blade Azure AD **Test voor een aanmelding.**
1. Selecteer op **de blade Test single sign-on** de optie Download the **SAML request**.
1. U ziet specifieke richtlijnen voor de oplossing op basis van de fout en de waarden in de SAML-aanvraag.
1. U ziet de knop **Herstellen om** de configuratie in Azure AD automatisch bij te werken om het probleem op te lossen. Als u deze knop niet ziet, wordt het aanmeldingsprobleem niet veroorzaakt door een onjuiste configuratie in Azure AD.

Als er geen oplossing wordt geboden voor de aanmeldingsfout, raden we u aan het feedbacktekstvak te gebruiken om ons hiervan op de hoogte te stellen.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>U kunt de fout oplossen zonder de Mijn apps-extensie voor beveiligde aanmelding te installeren

1. Kopieer het foutbericht in de rechteronderhoek van de pagina. Het foutbericht bevat:
    - Een CorrelationID en Timestamp. Deze waarden zijn belangrijk wanneer u een ondersteuningscase maakt bij Microsoft, omdat ze de technici helpen uw probleem te identificeren en een nauwkeurige oplossing voor uw probleem te bieden.
    - Een instructie die de hoofdoorzaak van het probleem identificeert.
1. Terug naar Azure AD en zoek de blade **Test voor een aanmelding.**
1. Plak het foutbericht in **het tekstvak** boven Richtlijnen voor oplossing krijgen.
1. Klik **op Richtlijnen voor het oplossen** van problemen om de stappen voor het oplossen van het probleem weer te geven. Voor de richtlijnen is mogelijk informatie uit de SAML-aanvraag of het SAML-antwoord vereist. Als u de Mijn apps-extensie voor beveiligde aanmelding niet gebruikt, hebt u mogelijk een hulpprogramma zoals [Fiddler](https://www.telerik.com/fiddler) nodig om de SAML-aanvraag en het antwoord op te halen.
1. Controleer of de bestemming in de SAML-aanvraag overeenkomt met de SAML Single Sign-On Service-URL die is verkregen van Azure AD.
1. Controleer of de verlener in de SAML-aanvraag dezelfde id is die u hebt geconfigureerd voor de toepassing in Azure AD. Azure AD gebruikt de vergever om een toepassing in uw directory te vinden.
1. Controleer of AssertionConsumerServiceURL de plaats is waar de toepassing het SAML-token verwacht te ontvangen van Azure AD. U kunt deze waarde configureren in Azure AD, maar deze is niet verplicht als deze deel uitmaakt van de SAML-aanvraag.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Een aanmeldingsfout op de toepassingspagina oplossen

U kunt zich mogelijk aanmelden en vervolgens een foutmelding zien op de pagina van de toepassing. Dit gebeurt wanneer Azure AD een token aan de toepassing heeft uitgegeven, maar de toepassing het antwoord niet accepteert.

Volg deze stappen om de fout op te lossen of bekijk deze korte video over het gebruik van Azure AD om problemen met [SAML SSO op te lossen:](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)

1. Als de toepassing zich in de Azure AD-galerie, controleert u of u alle stappen voor het integreren van de toepassing met Azure AD hebt gevolgd. Zie de lijst met zelfstudies voor [saas-toepassingsintegratie](../saas-apps/tutorial-list.md)voor de integratie-instructies voor uw toepassing.
1. Haal het SAML-antwoord op.
    - Als de Mijn apps secure sign-in-extensie is geïnstalleerd, klikt u op de blade Test op **het SAML-antwoord downloaden.** 
    - Als de extensie niet is geïnstalleerd, gebruikt u een hulpprogramma zoals [Fiddler om](https://www.telerik.com/fiddler) het SAML-antwoord op te halen.
1. Let op deze elementen in het SAML-antwoord token:
   - De unieke id van de gebruiker van de NameID-waarde en -indeling
   - Claims die zijn uitgegeven in het token
   - Certificaat dat wordt gebruikt om het token te ondertekenen.

     Zie SamL-protocol voor een aanmelding voor meer informatie over het [SAML-antwoord.](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

1. Nu u het SAML-antwoord hebt bekeken, ziet u [Fout](application-sign-in-problem-application-error.md) op de pagina van een toepassing na aanmelding voor hulp bij het oplossen van het probleem. 
1. Als u zich nog steeds niet kunt aanmelden, kunt u de leverancier van de toepassing vragen wat er ontbreekt in het SAML-antwoord.

## <a name="next-steps"></a>Volgende stappen

Nu een aanmelding werkt met uw toepassing, kunt u het inrichten en de inrichting van gebruikers voor [SaaS-toepassingen](../app-provisioning/user-provisioning.md) automatiseren of aan de slag gaan [met voorwaardelijke toegang.](../conditional-access/app-based-conditional-access.md)
