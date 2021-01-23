---
author: baanders
description: bestand insluiten voor het proces van het publiceren van een Azure-functie vanuit Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701153"
---
Als u het project wilt publiceren naar een functie-app in azure, klikt u met de rechter muisknop op het project in *Solution Explorer* en kiest u **publiceren**.

> [!IMPORTANT] 
> Wanneer u publiceert naar een functie-app in azure, worden extra kosten in rekening gebracht voor uw abonnement, onafhankelijk van Azure Digital Apparaatdubbels.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Scherm opname van Visual Studio met het menu met de rechter muisknop selecteren. Publiceren is gemarkeerd in het menu.":::

Laat op de pagina *Publish* die volgt, het standaard geselecteerde doel **Azure** staan en klik op *Next* (volgende). 

Kies voor een specifiek doel **Azure-functie-app (Windows)** en klik op *Next*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Scherm opname van Visual Studio in het dialoog venster Azure-functie publiceren. Azure functie-app (Windows) is geselecteerd op de specifieke doel pagina.":::

Kies uw abonnement op de pagina *Functions Instance*. Hiermee wordt een vak gevuld met de *resourcegroepen* in uw abonnement.

Selecteer de resourcegroep van uw exemplaar, en klik op *+* om een nieuwe Azure-functie maken.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Scherm opname van Visual Studio in het dialoog venster Azure-functie publiceren. De knop + om een nieuwe functie te maken, wordt op de pagina functie-exemplaar gemarkeerd.":::

Vul de velden in het venster *Function App (Windows) - Create new* (Functie-app (Windows) - Nieuwe maken) als volgt in:
* **Name** is de naam van het verbruiksabonnement dat Azure gebruikt om uw Azure Functions-app te hosten. Dit wordt ook de naam van de functie-app die uw werkelijke functie bevat. U kunt zelf een unieke waarde kiezen of de standaardsuggestie laten staan.
* Zorg ervoor dat het **Subscription** (abonnement) overeenkomt met het abonnement dat u wilt gebruiken 
* Zorg ervoor dat de **Resource group** overeenkomt met de resourcegroep die u wilt gebruiken
* Laat het **Plan type** staan op *Consumption* (verbruik)
* Selecteer de **Location** die overeenkomt met de locatie van uw resourcegroep
* Maak een nieuwe **Azure Storage**-resource met behulp van de koppeling *New...* . Stel de locatie in overeenkomstig uw resourcegroep, gebruik de standaardwaarden voor de overige velden en klik op OK.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Scherm opname van Visual Studio in het dialoog venster Azure-functie publiceren. De details van een nieuwe functie-app worden ingevuld, zoals naam, abonnement, resource groep, type abonnement, locatie en Azure Storage.":::

Ten slotte selecteert u **Create**.

Hiermee gaat u terug naar de pagina *Functions instance*, waar uw nieuwe functie-app nu onder uw resourcegroep wordt weergegeven. Klik op *Finish* (voltooien).

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure-functie publiceren in Visual Studio: Functions-instantie (na functie-app)":::

Controleer in het deelvenster *Publish* dat wordt geopend in het hoofdvenster van Visual Studio of alle gegevens er goed uitzien en selecteer **Publish**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Scherm opname van Visual Studio in het dialoog venster Azure-functie publiceren. De nieuwe functie-app wordt weer gegeven in de lijst met functie-apps en de knop volt ooien.":::

> [!NOTE]
> Als er een pop-upvenster wordt weer gegeven: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="scherm opname van Visual Studio pop-up met de naam Publish credentials. Het bevat velden voor een gebruikers naam en wacht woord en een knop om referenties op te halen uit Azure." border="false":::
> U selecteert **Attempt to retrieve credentials from Azure** (probeer referenties op te halen uit Azure) en **Save** (opslaan).
>
> Als er een waarschuwing wordt weergegeven dat *u de versie van Functions moet bijwerken op Azure* of dat *uw versie van de Functions-runtime niet overeenkomt met de versie die wordt uitgevoerd in Azure*:
>
> Volg de prompts om een upgrade uit te voeren naar de meest recente runtime-versie van Azure Functions. Dit probleem kan optreden als u een oudere versie van Visual Studio gebruikt.

De functie-app kan alleen toegang krijgen tot Azure Digital Apparaatdubbels als het een door een systeem beheerde identiteit heeft en toegang heeft tot uw Azure Digital Apparaatdubbels-exemplaar. Daarna stelt u dat in.
