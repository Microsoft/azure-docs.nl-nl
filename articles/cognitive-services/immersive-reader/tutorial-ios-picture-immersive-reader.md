---
title: 'Zelfstudie: Een iOS-app maken die een foto accepteert als invoer en deze weergeeft in de Insluitende lezer (Swift)'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie bouwt u een compleet nieuwe iOS-app en voegt u de functionaliteit 'Picture to Immersive Reader' toe.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 23e9fd586882d25aed646b984e17c5885145ccff
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284631"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Zelfstudie: Een iOS-app maken die de Insluitende lezer start met inhoud van een foto (Swift)

De [Insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulpprogramma waarmee bewezen technieken worden geïmplementeerd om de leesvaardigheid te verbeteren.

De [Read-API van Computer Vision Cognitive Services](../computer-vision/overview-ocr.md) detecteert tekstinhoud in een afbeelding met behulp van de nieuwste herkenningsmodellen van Microsoft en converteert de geïdentificeerde tekst naar een stroom tekens die kan worden gelezen door machines.

In deze zelfstudie maakt u een nieuwe iOS-app en integreert u de Read-API, en de Insluitende lezer met behulp van de Immersive Reader-SDK. U vindt [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios) een volledig werkend voorbeeld van deze zelfstudie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om deze in te stellen. U hebt enkele waarden nodig die u hier hebt gemaakt wanneer u de eigenschappen van het voorbeeldproject configureert. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* Om dit voorbeeld te gebruiken, hebt u een Azure-abonnement op de Computer Vision Cognitive Service nodig. [Maak een Computer Vision Cognitive Service-resource in de Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Maak een nieuw project in Xcode.

![Nieuw project](./media/ios/xcode-create-project.png)

Kies **Single View App**.

![Nieuwe app voor één weergave](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>De CocoaPods-SDK downloaden
De eenvoudigste manier om de Immersive Reader-SDK te gebruiken, is via CocoaPods. Installeren via Cocoapods:
1. [Installeer CocoaPods](http://guides.cocoapods.org/using/getting-started.html). Volg de beknopte handleiding om Cocoapods te installeren.
2. Maak een Podfile door `pod init` uit te voeren in de hoofdmap van uw Xcode-project.
3.  Voeg de CocoaPod toe aan uw Podfile door `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` toe te voegen. De Podfile moet er ongeveer zo uitzien, waarbij picture-to-immersive-reader-swift is vervangen door de naam van uw doel:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. Ga in the terminal naar de map van het Xcode-project en voer de opdracht `pod install` uit om de Immersive Reader-SDK-pod te installeren.
5. Voeg `import immersive_reader_sdk` toe aan alle bestanden die naar de SDK moeten verwijzen.
6. Open het project door het bestand `.xcworkspace` te openen, niet het bestand `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken verkrijgen

Voor dit onderdeel hebt u enkele waarden nodig uit de hierboven beschreven configuratie voor Azure AD-verificatie. Raadpleeg het tekstbestand dat u van die sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Maak in de hoofdmap van het project, met daarin het bestand ViewController.swift, een Swift-klassebestand met de naam Constants.swift. Vervang de klasse door de volgende code, waarbij u uw eigen waarden toevoegt waar van toepassing. Sla dit bestand alleen lokaal op uw computer op en zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat het geheimen bevat die niet openbaar moeten worden gemaakt. Het wordt aangeraden om geen geheimen te bewaren in uw app. In plaats daarvan kunt u beter een back-endservice gebruiken om het token te verkrijgen, omdat de geheimen dan buiten de app en het apparaat worden opgeslagen. Het API-eindpunt van de back-end moet worden beveiligd achter een bepaalde vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat niet-geautoriseerde gebruikers tokens verkrijgen die kunnen worden gebruikt voor uw Insluitende lezer-service en -facturering. Dit valt echter buiten het bereik van deze zelfstudie.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Instellen dat de app wordt uitgevoerd zonder een storyboard

Open AppDelegate.swift en vervang het bestand door de volgende code.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Functionaliteit toevoegen voor het maken en uploaden van foto's

Wijzig de naam van ViewController.swift in PictureLaunchViewController.swift en vervang het bestand door de volgende code.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Stel het archiefschema in Xcode in door een simulator of apparaatdoel te selecteren.
![Archiefschema](./media/ios/xcode-archive-scheme.png)<br/>
![Doel selecteren](./media/ios/xcode-select-target.png)

Druk in Xcode op Ctrl + R of klik op de afspeelknop om het project uit te voeren. De app wordt nu gestart in de opgegeven simulator of op het opgegeven apparaat.

In uw app ziet u het volgende:

![Voorbeeld-app](./media/ios/picture-to-immersive-reader-ipad-app.png)

Maak in de app een foto van tekst of upload een dergelijke foto door op de knop Take Photo of de knop Choose Photo from Library te klikken. De insluitende lezer wordt dan gestart en de tekst uit de foto wordt weergegeven.

![Insluitende lezer](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Volgende stappen

* Lees de [naslagdocumentatie voor de Immersive Reader-SDK](./reference.md).