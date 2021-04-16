---
author: baanders
description: bestand opnemen voor het publiceren van een Azure-functie vanuit Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480725"
---
Als u het project wilt publiceren naar een functie-app in Azure, begint u in Solution Explorer. Klik met de rechtermuisknop op het project en kies **publiceren.**

> [!IMPORTANT] 
> Voor het publiceren naar een functie-app in Azure worden extra kosten in rekening gebracht voor uw abonnement, onafhankelijk van Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Schermopname van Visual Studio, met het snelmenu van de oplossing. Publiceren is gemarkeerd in het menu.":::

Laat op **de** pagina Publiceren die wordt geopend de standaarddoelselectie **van Azure staan.** Selecteer vervolgens **Volgende**. 

Voor een specifiek doel kiest u **Azure-functie-app (Windows)** en selecteert u **vervolgens Volgende.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Schermopname van Visual Studio, met het dialoogvenster Azure-functie publiceren. Op de pagina Specifiek doel is de selectie Azure-functie-app (Windows).":::

Kies uw **abonnement op** het tabblad Functions-exemplaar. Selecteer vervolgens het pluspictogram (+) om een nieuwe functie te maken.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Schermopname van Visual Studio, met het dialoogvenster Azure-functie publiceren. Het pluspictogram is gemarkeerd.":::

Vul in **het venster Functie-app (Windows) - Nieuw** maken de volgende velden in:
* **Name** is de naam van het verbruiksabonnement dat Azure gebruikt om uw Azure Functions-app te hosten. Deze naam is ook van toepassing op de functie-app die uw werkelijke functie bevat. U kunt een unieke waarde kiezen of de standaardsuggestie laten staan.
* Zorg ervoor dat **het Abonnement** overeenkomt met het abonnement dat u wilt gebruiken. 
* Zorg ervoor dat **de resourcegroep** de resourcegroep is die u wilt gebruiken.
* Laat de **selectie Plantype** op **Verbruik.**
* Selecteer de **Locatie** van uw resourcegroep.
* Maak een nieuwe **Azure Storage** resource door de koppeling **Nieuw te** selecteren. Stel de locatie in die bij uw resourcegroep past, gebruik de andere standaardwaarden en selecteer **ok.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Schermopname van Visual Studio, met het dialoogvenster Azure-functie publiceren. De details van een nieuwe functie-app worden ingevuld, waaronder Naam, Abonnement, Resourcegroep, Plantype, Locatie en Azure Storage.":::

Selecteer vervolgens **Maken**.

Nadat de app-service is gemaakt, wordt het **tabblad Functions-exemplaar** geopend. De nieuwe functie-app wordt weergegeven in het **gebied Functie-apps** onder de resourcegroep. Selecteer **Finish**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Schermopname van Visual Studio, met het dialoogvenster Azure-functie publiceren. Het tabblad Functions-exemplaar is geselecteerd. De nieuwe functie-app wordt weergegeven onder de resourcegroep.":::

Controleer in **het** deelvenster Publiceren dat wordt geopend in Visual Studio hoofdvenster of alle informatie er correct uitziet. Selecteer vervolgens **Publiceren**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Schermopname van Visual Studio, met het deelvenster Publiceren. De knop Publiceren is gemarkeerd.":::

> [!NOTE]
> Als u een pop-upvenster ziet zoals in het volgende voorbeeld, selecteert u **Poging om** referenties op te halen uit Azure en selecteert u **vervolgens Opslaan.**
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Schermopname van Visual Studio, met een pop-upvenster met de naam Referenties publiceren. Deze bevat velden voor een gebruikersnaam en wachtwoord. Het bevat ook een knop om te proberen referenties op te halen uit Azure." border="false":::
>
> Als u een van de volgende waarschuwingen ziet, volgt u de aanwijzingen om een upgrade uit te voeren naar de meest recente Azure Functions runtime-versie:
> * 'Upgrade Functions version on Azure'.
> * 'Uw versie van de Functions-runtime komt niet overeen met de versie die wordt uitgevoerd in Azure.'
>
> Deze waarschuwingen kunnen worden weergegeven als u een oude versie van Visual Studio.

Uw functie-app is nu gepubliceerd in Azure.
