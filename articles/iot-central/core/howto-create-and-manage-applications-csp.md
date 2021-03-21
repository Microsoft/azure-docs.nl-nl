---
title: Azure IoT Central-toepassingen maken en beheren vanuit de CSP-Portal | Microsoft Docs
description: Als CSP kunt u een Azure IoT Central-toepassing maken namens uw klant.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675804"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Een Azure IoT Central-toepassing maken en beheren vanuit de CSP-Portal

Het programma voor de Microsoft Cloud Solution Provider (CSP) is een micro soft-programma voor wederverkopers. Het is de bedoeling om onze kanaal partners te voorzien van een programma met één stop om alle micro soft-commerciële online services door te verkopen. Meer informatie over het [programma Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Als CSP kunt u Microsoft Azure IoT Central-toepassingen maken en beheren namens uw klanten via het [micro soft partner centrum](https://partnercenter.microsoft.com/partner/home). Wanneer Azure IoT Central-toepassingen worden gemaakt namens klanten door Csp's, net als bij andere door de CSP beheerde Azure-Services, beheren Csp's facturering voor klanten. Kosten voor Azure IoT Central worden weer gegeven in uw factuur totaal in het micro soft partner centrum.

Meld u aan bij uw account in de micro soft-Partner Portal en selecteer een klant voor wie u een Azure IoT Central-toepassing wilt maken. Navigeer naar Service beheer voor de klant in het linkernavigatievenster.

![Micro soft Partner Center, klant weergave](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central wordt vermeld als een service die beschikbaar is voor beheer. Selecteer de koppeling Azure IoT Central op de pagina om nieuwe toepassingen te maken of bestaande toepassingen voor deze klant te beheren.

![Azure IoT Central beschikbaar voor beheer](media/howto-create-and-manage-applications-csp/image2.png)

U bent op de pagina Azure IoT Central Application Manager. Azure IoT Central houdt u de context van het micro soft partner centrum en u hebt deze gearriveerd om die bepaalde klant te beheren. U ziet dit bevestigd in de koptekst van de pagina Toepassings beheer. Hier kunt u navigeren naar een bestaande toepassing die u eerder hebt gemaakt voor deze klant om een nieuwe toepassing voor de klant te beheren of maken.

![Manager voor Csp's maken](media/howto-create-and-manage-applications-csp/image3.png)

Als u een Azure IoT Central-toepassing wilt maken, selecteert u **samen stellen** in het menu links. Kies een van de branche sjablonen of kies **aangepaste app** om een volledig nieuwe toepassing te maken. Hiermee wordt de pagina voor het maken van de toepassing geladen. U moet alle velden op deze pagina volt ooien en vervolgens **maken** kiezen. U vindt meer informatie over elk van de onderstaande velden.

![Scherm opname van de pagina ' uw IoT-toepassing bouwen ' met de knop opbouwen geselecteerd.](media/howto-create-and-manage-applications-csp/image4.png)

![Toepassings pagina voor Csp's maken](media/howto-create-and-manage-applications-csp/image4-1.png)

![Een toepassings pagina maken voor Csp's facturerings gegevens](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Prijs plan

U kunt alleen toepassingen maken die gebruikmaken van een standaard prijs plan als CSP. Als u Azure IoT Central wilt presen teren aan uw klant, kunt u een toepassing maken die gebruikmaakt van het gratis prijs plan afzonderlijk. Meer informatie over de gratis en standaardabonnementen vindt u op de [pagina met prijzen voor Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

U kunt alleen toepassingen maken die gebruikmaken van een standaard prijs plan als CSP. Als u Azure IoT Central wilt presen teren aan uw klant, kunt u een toepassing maken die gebruikmaakt van het gratis prijs plan afzonderlijk. Meer informatie over de gratis en standaardabonnementen vindt u op de [pagina met prijzen voor Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>De naam van de toepassing

De naam van uw toepassing wordt weer gegeven op de pagina **toepassings beheer** en binnen elke Azure IOT Central-toepassing. U kunt een wille keurige naam kiezen voor uw Azure IoT Central-toepassing. Kies een naam die voor u en anderen in uw organisatie zinvol is.

## <a name="application-url"></a>Toepassings-URL

De toepassings-URL is de koppeling naar uw toepassing. U kunt een blad wijzer in uw browser opslaan of met anderen delen.

Wanneer u de naam voor uw toepassing opgeeft, wordt de URL van de toepassing automatisch gegenereerd. Als u wilt, kunt u een andere URL voor uw toepassing kiezen. Elke URL van Azure IoT Central moet uniek zijn binnen Azure IoT Central. Er wordt een fout bericht weer gegeven als de URL die u kiest al is gemaakt.

## <a name="directory"></a>Directory

Omdat Azure IoT Central context heeft dat u de klant die u hebt geselecteerd in de micro soft-Partner Portal hebt beheerd, ziet u alleen de Azure Active Directory Tenant voor die klant in het veld Directory. 

Een Azure Active Directory-Tenant bevat gebruikers identiteiten, referenties en andere informatie over de organisatie. Meerdere Azure-abonnementen kunnen worden gekoppeld aan één Azure Active Directory Tenant.

Zie [Azure Active Directory](../../active-directory/index.yml)voor meer informatie.

## <a name="azure-subscription"></a>Azure-abonnement

Met een Azure-abonnement kunt u exemplaren maken van Azure-services. Azure IoT Central detecteert automatisch alle Azure-abonnementen van de klant naar wie u toegang hebt en geeft deze weer in een vervolg keuzelijst op de pagina **toepassing maken** . Kies een Azure-abonnement om een nieuwe Azure IoT Central-toepassing te maken.

Als u geen Azure-abonnement hebt, kunt u er een maken in het micro soft partner centrum. Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Toepassing maken**. Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.

Zie [Azure-abonnementen](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)voor meer informatie.

## <a name="location"></a>Locatie

De **locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Gewoonlijk kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om de beste prestaties te verkrijgen. Op dit moment kunt u een IoT Central-toepassing maken in de geografs **Australia**, **Azië en Stille Oceaan**, **Europa**, **Verenigde Staten**, het **Verenigd Konink rijk** en **Japan** . Als u eenmaal een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

## <a name="application-template"></a>Toepassingsjabloon

Kies de toepassings sjabloon die u wilt gebruiken voor uw toepassing.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Azure IoT Central-toepassing maakt als CSP, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)