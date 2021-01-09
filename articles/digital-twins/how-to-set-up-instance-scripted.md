---
title: Een exemplaar en verificatie instellen (met een script)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen door een script voor automatische implementatie uit te voeren
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1667c21b9a35b8e93feffb8cf1b37d4409da73c3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044302"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie instellen (met een script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel voert u deze stappen uit door een voor beeld van een [ **geautomatiseerd implementatie script**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) uit te voeren dat het proces stroomlijnt. 
* Zie de CLI-versie van dit artikel voor informatie over de hand matige CLI-stappen die door het script worden uitgevoerd achter de schermen: [*instructies: een exemplaar en authenticatie instellen (CLI)*](how-to-set-up-instance-cli.md).
* Als u de hand matige stappen wilt bekijken volgens de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Vereisten: het script downloaden

Het voorbeeld script is geschreven in Power shell. Het maakt deel uit van de [**end-to-end-voor beelden van Azure Digital apparaatdubbels**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), die u kunt downloaden naar uw computer door te navigeren naar de voorbeeld koppeling en de knop *zip downloaden* te selecteren onder de titel.

Hiermee wordt het voorbeeld project gedownload naar uw computer als _**Azure_Digital_Twins_end_to_end_samples.zip**_. Ga naar de map op de computer en pak deze uit om de bestanden uit te pakken.

Het implementatie script bevindt zich in de map ungezipte op _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Het implementatiescript uitvoeren

In dit artikel wordt een voor beeld van een Azure Digital Apparaatdubbels-code gebruikt om een Azure Digital Apparaatdubbels-exemplaar en de vereiste verificatie semi-automatisch te implementeren. Het kan ook worden gebruikt als uitgangs punt voor het schrijven van uw eigen interscript-interacties.

Hier volgen de stappen voor het uitvoeren van het implementatie script in Cloud Shell.
1. Ga naar een [Azure Cloud shell](https://shell.azure.com/) -venster in uw browser. Meld u aan met deze opdracht:
    ```azurecli-interactive
    az login
    ```
    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen. Als dat niet het geval is, opent u een browserpagina op *https://aka.ms/devicelogin* en voert u de autorisatiecode in die wordt weergegeven in de terminal.
 
2. Controleer in de Cloud Shell pictogram balk of uw Cloud Shell is ingesteld op het uitvoeren van de Power shell-versie.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell venster met de selectie van de Power shell-versie":::

1. Selecteer het pictogram bestanden uploaden/downloaden en kies uploaden.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell venster met de selectie van het pictogram uploaden":::

    Ga naar het _**deploy.ps1**_ bestand op uw computer (in _Azure_Digital_Twins_end_to_end_samples > scripts > **deploy.ps1**_) en klik op openen. Hiermee wordt het bestand geüpload naar Cloud Shell, zodat u het kunt uitvoeren in het Cloud Shell-venster.

4. Voer het script uit door de `./deploy.ps1` opdracht in het venster Cloud shell te verzenden. U kunt de onderstaande opdracht kopiëren (intrekken om te plakken in Cloud Shell, u kunt **CTRL + SHIFT + v** op Windows en Linux gebruiken of **Cmd + Shift + v** op macOS. U kunt ook het menu met de rechter muisknop gebruiken).

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Met het script wordt een Azure Digital Apparaatdubbels-exemplaar gemaakt en wordt uw Azure-gebruiker de rol *Azure Digital apparaatdubbels-gegevens eigenaar* voor het exemplaar toegewezen.

    Wanneer het script wordt uitgevoerd via de automatische installatie stappen, wordt u gevraagd de volgende waarden door te geven:
    * Voor het exemplaar: de *abonnements-id* van uw Azure-abonnement dat moet worden gebruikt
    * Voor het exemplaar: een *locatie* waar u het exemplaar wilt implementeren. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Voor het exemplaar: een naam van een *resource groep* . U kunt een bestaande resource groep gebruiken of een nieuwe naam opgeven om deze te maken.
    * Voor het exemplaar: een *naam* voor uw Azure Digital apparaatdubbels-exemplaar. Als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio waarin de opgegeven naam al wordt gebruikt, wordt u gevraagd een andere naam te kiezen.

Hier volgt een fragment van het uitvoer logboek van het script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell venster met het logboek van de invoer en uitvoer via het uitvoeren van het script implementeren" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Als het script is voltooid, wordt de uiteindelijke afdruk weer te zeggen `Deployment completed successfully` . Als dat niet het geval is, adresseert u het fout bericht en voert u het script opnieuw uit. De stappen die u al hebt voltooid, worden overgeslagen en de invoer wordt opnieuw gestart op het punt waar u was gebleven.

> [!NOTE]
> Met het script wordt momenteel de vereiste beheer functie in azure Digital Apparaatdubbels (*Azure Digital Apparaatdubbels data owner*) toegewezen aan dezelfde gebruiker die het script uitvoert vanuit Cloud shell. Als u deze rol moet toewijzen aan iemand anders die het exemplaar gaat beheren, kunt u dit nu doen via de Azure Portal ([instructies](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) of cli ([instructies](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>Er is momenteel een **bekend probleem** met het instellen van een script, waarbij sommige gebruikers (met name gebruikers van persoonlijke [micro soft-accounts (msa's)](https://account.microsoft.com/account)) de roltoewijzing kunnen vinden voor de eigenaar van de **_Azure Digital apparaatdubbels-gegevens_ die niet is gemaakt**.
>
>U kunt de roltoewijzing controleren met het gedeelte [*toewijzing*](#verify-user-role-assignment) van gebruikersrol controleren verderop in dit artikel, en, indien nodig, de roltoewijzing hand matig instellen met behulp van de [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) of [cli](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Zie [*Problemen oplossen: Bekende problemen in Azure Digital Twins*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup) voor meer informatie hierover.

## <a name="verify-success-and-collect-important-values"></a>Geslaagde pogingen controleren en belang rijke waarden verzamelen

Als u wilt controleren of uw resources en machtigingen zijn ingesteld door het script, kunt u deze bekijken in de [Azure Portal](https://portal.azure.com) met de onderstaande instructies. Als u het succes van een stap niet kunt controleren, voert u de stap opnieuw uit. U kunt de stappen afzonderlijk uitvoeren met behulp van de [Azure Portal](how-to-set-up-instance-portal.md) -of [cli](how-to-set-up-instance-cli.md) -instructies.

In deze sectie wordt ook uitgelegd hoe u belang rijke waarden vindt in de resources die zijn ingesteld door het script dat u mogelijk nodig hebt, omdat u verder met uw Azure Digital Apparaatdubbels-exemplaar kunt werken. U moet deze waarden opslaan op een veilige plaats of terugkeren naar deze sectie om ze later te vinden wanneer u ze nodig hebt. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden ook met hen delen.

### <a name="verify-instance"></a>Exemplaar controleren

Als u wilt controleren of uw exemplaar is gemaakt, gaat u naar de [pagina Azure Digital apparaatdubbels](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) in de Azure Portal. U kunt deze pagina zelf raadplegen door te zoeken naar *Azure Digital apparaatdubbels* in de zoek balk van de portal.

Op deze pagina vindt u een lijst met alle Azure Digital Apparaatdubbels-instanties. Zoek naar de naam van uw nieuw gemaakte exemplaar in de lijst.

Als de verificatie is mislukt, kunt u het opnieuw proberen om een exemplaar te maken met de [Portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) of de [cli](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Instantie waarden verzamelen

Selecteer de naam van uw exemplaar op de [pagina Azure Digital apparaatdubbels](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) om de *overzichts* pagina van het exemplaar te openen. Noteer de *naam*, de *resource groep* en de *hostnaam*. U hebt deze mogelijk later nodig om uw exemplaar te identificeren en er verbinding mee te maken.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markeren van de belang rijke waarden van de overzichts pagina van het exemplaar":::

### <a name="verify-user-role-assignment"></a>Gebruikersrol toewijzing controleren

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> U herinnert dat het script momenteel deze vereiste rol toewijst aan dezelfde gebruiker die het script uitvoert van Cloud Shell. Als u deze rol moet toewijzen aan iemand anders die het exemplaar gaat beheren, kunt u dit nu doen via de Azure Portal ([instructies](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) of cli ([instructies](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Als de verificatie is mislukt, kunt u ook uw eigen roltoewijzing opnieuw uitvoeren met behulp van de [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) of [cli](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Volgende stappen

Test afzonderlijke REST API-aanroepen voor uw exemplaar met behulp van de Azure Digital Apparaatdubbels CLI-opdrachten: 
* [AZ DT-referentie](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Instructies: De Azure Digital Twins-CLI gebruiken*](how-to-use-cli.md)

U kunt ook zien hoe u een client toepassing verbindt met uw exemplaar met verificatie code:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)