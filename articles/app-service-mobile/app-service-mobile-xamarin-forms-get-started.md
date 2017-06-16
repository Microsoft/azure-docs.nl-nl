---
title: Aan de slag met Mobile Apps met behulp van Xamarin.Forms
description: Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Apps voor Xamarin.Forms-ontwikkeling
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 6b8c5c2ac2e721c4d6f73c7c17f34eadc041e0c9
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-xamarinforms-app"></a>Een Xamarin.Forms-app maken
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-windows-selector-get-started.md)]

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Xamarin.Forms-app met een back-end voor Azure Mobile Apps. U maakt zowel een nieuwe back-end voor Mobile Apps als een eenvoudige Xamarin.Forms-app voor *takenlijsten* die app-gegevens opslaat in Azure.

Het voltooien van deze zelfstudie is een vereiste voor alle andere zelfstudies over Mobile Apps voor Xamarin.Forms.

## <a name="prerequisites"></a>Zelfstudi
Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal tien gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Visual Studio met Xamarin. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) voor instructies. 
* Een Mac met Xcode v7.0 of hoger en waarop Xamarin Studio Community is geïnstalleerd. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) en [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Instructies voor installatie, configuratie en verificatie voor Mac-gebruikers) (MSDN).

> [!NOTE]
> Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://azure.microsoft.com/try/app-service/mobile/). Hier kunt u direct een tijdelijke en eenvoudige Mobile App maken in App Service. U hebt geen creditcard nodig en u gaat geen verplichtingen aan.
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>Een nieuwe back-end voor mobiele apps van Azure maken
Volg deze stappen voor het maken van een nieuwe back-end voor mobiele apps.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

U hebt nu een back-end voor mobiele apps van Azure ingericht, die kan worden gebruikt door uw mobiele-clienttoepassingen. Nu gaat u een serverproject downloaden voor een eenvoudige back-end voor takenlijsten en deze publiceren naar Azure.

## <a name="configure-the-server-project"></a>Het serverproject configureren
Volg onderstaande stappen voor het configureren van het serverproject voor het gebruik van de Node.js- of .NET-back-end.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>De Xamarin.Forms-oplossing downloaden en uitvoeren
U hebt hier een aantal opties. U kunt de oplossing downloaden naar een Mac en openen in Xamarin Studio of u kunt de oplossing downloaden naar een Windows-computer en openen in Visual Studio met een Mac in een netwerk voor het bouwen van de iOS-app. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) als u meer gedetailleerde instructies voor de Xamarin-installatiescenario's nodig hebt.

Laten we beginnen:

1. Op uw Mac of Windows-computer opent u [Azure Portal] in een browservenster.
2. Klik op de blade Instellingen voor uw mobiele app op **Aan de slag** (onder Mobiel) > **Xamarin.Forms**. Klik in stap 3 op  **Een nieuwe app maken** als deze optie nog niet is geselecteerd.  Klik vervolgens op de knop **Downloaden**.
   
   Er wordt een project gedownload dat een clienttoepassing bevat die is verbonden met uw mobiele app. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.
3. Pak het project uit dat u hebt gedownload en open het in Xamarin Studio of Visual Studio.
   
   ![][9]
   
   ![][8]

## <a name="optional-run-the-ios-project"></a>(Optioneel) Het iOS-project uitvoeren
Deze sectie gaat over het uitvoeren van het Xamarin iOS-project voor iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overslaan.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio
1. Klik met de rechtermuisknop op het iOS-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Uitvoeren** op **Foutopsporing starten** om het project te bouwen en de app te starten in de iPhone-emulator.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Klik met de rechtermuisknop op het iOS-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Bouwen** op **Configuration Manager**.
3. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** van het iOS-project in.
4. Druk op de toets **F5** om het project te bouwen en de app te starten in de iPhone-emulator.
   
    [!NOTE]
    Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    
    
   

Typ in de app zinvolle tekst, zoals *Xamarin leren kennen*, en klik vervolgens op de knop **+**.

![][10]

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor mobiele apps in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.
> 
> 

## <a name="optional-run-the-android-project"></a>(Optioneel) Het Android-project uitvoeren
Deze sectie gaat over het uitvoeren van het Xamarin Droid-project voor Android-apparaten. Als u niet met Android-apparaten werkt, kunt u deze sectie overslaan.

#### <a name="in-xamarin-studio"></a>In Xamarin Studio
1. Klik met de rechtermuisknop op het Android-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Uitvoeren** op **Foutopsporing starten** om het project te bouwen en de app te starten in een Android-emulator.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Klik met de rechtermuisknop op het Android-project (Droid) en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Bouwen** op **Configuration Manager**.
3. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** van het Android-project in.
4. Druk op de toets **F5** om het project te bouwen en de apps te starten in een Android-emulator.
   
   > [!NOTE]
   > Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    
   > 
   > 

Typ in de app zinvolle tekst, zoals *Xamarin leren kennen*, en klik vervolgens op de knop **+**.

![][11]

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor mobile apps in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.
> 
> 

## <a name="optional-run-the-windows-project"></a>(Optioneel) Het Windows-project uitvoeren
Deze sectie gaat over het uitvoeren van het project Xamarin WinApp voor Windows-apparaten. Als u niet met Windows-apparaten werkt, kunt u deze sectie overslaan.

#### <a name="in-visual-studio"></a>In Visual Studio
1. Klik met de rechtermuisknop op een van de Windows-projecten en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Bouwen** op **Configuration Manager**.
3. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** in van het Windows-project dat u hebt gekozen.
4. Druk op de toets **F5** om het project te bouwen en de apps te starten in een Windows-emulator.
   
   > [!NOTE]
   > Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    
   > 
   > 

Typ in de app zinvolle tekst, zoals *Xamarin leren kennen*, en klik vervolgens op de knop **+**.

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

![][12]

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor mobiele apps in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Verificatie toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.
* [Pushmeldingen toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-push.md)  
  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.
* [De beheerde client gebruiken voor Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Informatie over het werken met SDK voor beheerde clients in uw Xamarin-app. 

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/


