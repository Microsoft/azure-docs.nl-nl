---
title: De Azure IoT Central-gebruikers interface aanpassen | Microsoft Docs
description: Het thema en de Help-koppelingen voor uw Azure IoT Central-toepassing aanpassen
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90019832"
---
# <a name="customize-the-azure-iot-central-ui"></a>De Azure IoT Central-gebruikers interface aanpassen

In dit artikel wordt beschreven hoe u als beheerder de gebruikers interface van uw toepassing kunt aanpassen door aangepaste Thema's toe te passen en de Help-koppelingen te wijzigen zodat deze naar uw eigen aangepaste Help-bronnen verwijzen. 

Op de volgende scherm afbeelding ziet u een pagina met het standaard thema:

![Standaard IoT Central-thema](./media/howto-customize-ui/standard-ui.png)

In de volgende scherm afbeelding ziet u een pagina met behulp van een aangepaste scherm opname waarin de aangepaste UI-elementen zijn gemarkeerd:

![Aangepast IoT Central thema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Thema maken

Als u een aangepast thema wilt maken, gaat u naar de pagina **uw toepassing aanpassen** in de sectie **beheer** :

![IoT Central-Thema's](./media/howto-customize-ui/themes.png)

Op deze pagina kunt u de volgende aspecten van uw toepassing aanpassen:

### <a name="application-logo"></a>Toepassings logo

Een PNG-afbeelding, niet groter dan 1 MB, met een transparante achtergrond. Dit logo wordt links op de titel balk van de IoT Central toepassing weer gegeven.

Als uw logo afbeelding de naam van uw toepassing bevat, kunt u de tekst van de toepassings naam verbergen. Zie [uw toepassing beheren](howto-administer.md#change-application-name-and-url)voor meer informatie.

### <a name="browser-icon-favicon"></a>Browser pictogram (favicon)

Een PNG-afbeelding, niet groter dan 32 x 32 pixels, met een transparante achtergrond. Een webbrowser kan deze installatie kopie gebruiken in de adres balk, de geschiedenis, de blad wijzers en het browser tabblad.

### <a name="browser-colors"></a>Browser kleuren

U kunt de kleur van de paginakop tekst en de kleur die wordt gebruikt voor accent knoppen en andere hooglichten wijzigen. Gebruik een hexadecimale kleur waarde van zes tekens in de notatie `##ff6347` . Zie [HTML-kleuren](https://www.w3schools.com/html/html_colors.asp)voor meer informatie over de kleur notatie **hex-waarde** .

> [!NOTE]
> Op de pagina **uw toepassing aanpassen** kunt u altijd terugkeren naar de standaard opties.

### <a name="changes-for-operators"></a>Wijzigingen voor Opera tors

Als een beheerder een aangepast thema maakt, kunnen Opera tors en andere gebruikers van uw toepassing niet meer een thema in **instellingen** kiezen.

## <a name="replace-help-links"></a>Help-koppelingen vervangen

Als u aangepaste Help-informatie voor uw Opera tors en andere gebruikers wilt opgeven, kunt u de koppelingen in het **Help** -menu van de toepassing wijzigen.

Als u de Help-koppelingen wilt wijzigen, gaat u naar de pagina **Help aanpassen** in de sectie **beheer** :

![Help-koppelingen voor IoT Central aanpassen](./media/howto-customize-ui/help-links.png)

U kunt ook nieuwe vermeldingen toevoegen aan het menu Help en standaard vermeldingen verwijderen:

![Help voor aangepaste IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> U kunt altijd terugkeren naar de standaard Help-koppelingen op de Help-pagina **aanpassen** .

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de gebruikers interface in uw IoT Central-toepassing kunt aanpassen, volgt u een aantal voorgestelde volgende stappen:

- [Uw toepassing beheren](./howto-administer.md)
- [Tegels toevoegen aan uw dashboard](howto-add-tiles-to-your-dashboard.md)