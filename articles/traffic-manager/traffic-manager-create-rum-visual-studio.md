---
title: Real-user-metingen met Visual Studio Mobile Center-Azure Traffic Manager
description: Stel uw mobiele toepassing in die is ontwikkeld met behulp van Visual Studio Mobile Center om Real-user-metingen te verzenden naar Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: f9e8cdd3eb5c9f441444683fb5efaccc880b2757
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184608"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Real-user-metingen verzenden naar Traffic Manager met Visual Studio Mobile Center

U kunt uw mobiele toepassing die is ontwikkeld met behulp van Visual Studio Mobile Center zo instellen dat u Real-user-metingen naar Traffic Manager kunt verzenden door de volgende stappen te volgen:

>[!NOTE]
> Het verzenden van Real-user-metingen naar Traffic Manager wordt momenteel alleen ondersteund voor Android.

Als u Real-user-metingen wilt configureren, moet u een sleutel en instrument voor uw app verkrijgen met het RUM-pakket.

## <a name="step-1-obtain-a-key"></a>Stap 1: een sleutel verkrijgen
    
De metingen die u onderneemt en naar Traffic Manager verzendt vanuit uw client toepassing, worden geïdentificeerd door de service met een unieke teken reeks, de Real-user-metingen (RUM) sleutel genoemd. U kunt een RUM-sleutel ophalen met behulp van de Azure Portal, een REST API of door gebruik te maken van de Power shell/CLI-interfaces.

Gebruik de volgende procedure om de sleutel RUM te verkrijgen met Azure Portal:
1. Meld u vanuit een browser aan bij Azure Portal. Als u nog geen account hebt, kunt u zich registreren voor een gratis proefversie van één maand.
2. Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op **real-User-metingen** onder **instellingen** op de pagina Profiel Traffic Manager.
4. Klik op **sleutel genereren** om een nieuwe sleutel rum te maken.
        
   ![Real-user-metingen sleutel genereren](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Afbeelding 1: genereren van sleutels Real-user-metingen**

5. Op de pagina worden de gegenereerde RUM-sleutel en een Java script-code fragment weer gegeven dat moet worden inge sloten in de HTML-pagina.
 
   ![Java script-code voor Real-user-metingen sleutel](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Afbeelding 2: Real-user-metingen Key en meting java script**
 
6. Klik op de knop **kopiëren** om de sleutel rum te kopiëren. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Stap 2: uw app instrumenteren met het RUM-pakket van de Mobile Center SDK

Als u niet bekend bent met Visual Studio Mobile Center, gaat u naar de bijbehorende [website](https://mobile.azure.com). Zie [aan de slag met de Android-SDK](/mobile-center/sdk/getting-started/Android)voor meer informatie over de integratie van de SDK.

Als u Real-user-metingen wilt gebruiken, voert u de volgende procedure uit:

1.  De SDK toevoegen aan het project

    Tijdens de preview-versie van de ATM RUM SDK moet u expliciet verwijzen naar de pakket opslagplaats.

    Voeg in uw **app/build. gradle** -bestand de volgende regels toe:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Voeg in uw **app/build. gradle** -bestand de volgende regels toe:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. De SDK starten

    Open de belangrijkste activiteiten klasse van uw app en voeg de volgende import instructies toe:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Zoek naar de `onCreate` call back in hetzelfde bestand en voeg de volgende code toe:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [real-User-metingen](traffic-manager-rum-overview.md)
- Meer informatie [over de werking van Traffic Manager](traffic-manager-overview.md)
- Meer informatie over [mobiel centrum](/mobile-center/)
- [Registreren](https://mobile.azure.com) voor mobiel centrum
- Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund
- Meer informatie over het [maken van een Traffic Manager profiel](./quickstart-create-traffic-manager-profile.md)