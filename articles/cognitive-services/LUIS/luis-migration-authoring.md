---
title: Migreren naar een Azure-resource bewerkings sleutel
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u Language Understanding (LUIS) authoring-verificatie van een e-mail account naar een Azure-resource migreert.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787634"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migreren naar een Azure-resource bewerkings sleutel

> [!IMPORTANT]
>  Vanaf 3 december moeten bestaande LUIS-gebruikers het migratie proces volt ooien om de LUIS-toepassingen te blijven ontwerpen.

Language Understanding-ontwerp verificatie (LUIS) is gewijzigd van een e-mail account in een Azure-resource. Gebruik dit artikel voor meer informatie over het migreren van uw account, als u nog niet hebt gemigreerd.  


## <a name="what-is-migration"></a>Wat is migratie?

Migratie is het proces van het wijzigen van de ontwerp verificatie van een e-mail account in een Azure-resource. Uw account wordt gekoppeld aan een Azure-abonnement en een Azure-ontwerp bron nadat u de migratie hebt gemigreerd.

Migratie moet worden uitgevoerd vanuit de [Luis-Portal](https://www.luis.ai). Als u de ontwerp sleutels maakt met behulp van de LUIS CLI, moet u bijvoorbeeld het migratie proces in de LUIS-Portal volt ooien. U kunt nog steeds co-auteurs op uw toepassingen hebben na de migratie, maar deze worden toegevoegd op het Azure-resource niveau in plaats van op toepassings niveau. Het migreren van uw account kan niet ongedaan worden gemaakt.

> [!Note]
> * Als u een Voorspellings runtime-resource moet maken, is er [een afzonderlijk proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) om het te maken.
> * Zie de sectie [migratie notities](#migration-notes) hieronder voor informatie over hoe uw toepassingen en mede werkers worden beïnvloed. 
> * Het is gratis om uw LUIS-app te ontwerpen, zoals wordt aangegeven door de laag F0. Meer informatie [over prijs categorieën](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Migratie vereisten

* Een geldig Azure-abonnement. Vraag uw Tenant beheerder om u toe te voegen aan het abonnement of Meld u aan [voor een gratis account](https://azure.microsoft.com/free/cognitive-services).
* Een LUIS Azure-ontwerp bron van de LUIS-portal of van de [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * Het maken van een ontwerp bron vanuit de LUIS-Portal maakt deel uit van het migratie proces dat in de volgende sectie wordt beschreven.
* Als u een samen werker van toepassingen bent, worden toepassingen niet automatisch gemigreerd. U wordt gevraagd deze apps te exporteren tijdens de migratie stroom. U kunt ook de [export-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)gebruiken. U kunt de app na de migratie weer in LUIS importeren. Het import proces maakt een nieuwe app met een nieuwe app-ID waarvoor u de eigenaar bent.        
* Als u de eigenaar van de toepassing bent, hoeft u uw apps niet te exporteren omdat deze automatisch worden gemigreerd. Er wordt een e-mail sjabloon met een lijst met alle samen werkers voor elke toepassing verstrekt, zodat ze op de hoogte kunnen worden gesteld van het migratie proces.

## <a name="migration-steps"></a>Migratiestappen

1. Wanneer u zich aanmeldt bij de [Luis-Portal](https://www.luis.ai), wordt er een Azure Migration window geopend met de stappen voor migratie. Als u deze negeert, kunt u niet door gaan met het ontwerpen van uw LUIS-toepassingen. de enige weer gegeven actie is om door te gaan met de migratie.

    > [!div class="mx-imgBorder"]
    > ![Inleiding tot migratie venster](./media/migrate-authoring-key/notify-azure-migration.png)

2. Als u deel nemers hebt voor een van uw apps, ziet u een lijst met toepassings namen die eigendom zijn van u, samen met de ontwerp regio en e-mail berichten van de mede werker van elke toepassing. We raden u aan uw mede werkers een e-mail te sturen over de migratie door te klikken op de knop symbool **verzenden** aan de linkerkant van de toepassings naam.
Er `*` wordt een symbool naast de naam van de toepassing weer gegeven als een samen werker een Voorspellings resource heeft die aan uw toepassing is toegewezen. Na de migratie hebben deze apps nog steeds deze Voorspellings bronnen toegewezen, zelfs als de mede werkers geen toegang hebben tot het ontwerpen van uw toepassingen. Deze toewijzing wordt echter verbroken als de eigenaar van de Voorspellings bron [de sleutels](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) uit de Azure Portal opnieuw heeft gegenereerd.  

   > [!div class="mx-imgBorder"]
   > ![Mede werkers informeren](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Voor elke samen werker en app wordt de standaard e-mail toepassing geopend met een e-mail met een lichte opmaak. U kunt de e-mail bewerken voordat u deze verzendt. De e-mail sjabloon bevat de exacte App-ID en app-naam.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Nadat u uw account naar Azure hebt gemigreerd, zijn uw apps niet langer beschikbaar voor deel nemers.

3. Als u een samen werker op alle apps bent, wordt een lijst met toepassings namen die met u zijn gedeeld weer gegeven samen met de ontwerp regio en de e-mail berichten van de eigenaar van elke toepassing. U wordt aangeraden een kopie van de apps te exporteren door te klikken op de knop exporteren links van de naam van de toepassing. U kunt deze apps weer importeren nadat u bent gemigreerd, omdat ze niet automatisch met u worden gemigreerd.
Er `*` wordt een symbool weer gegeven naast de naam van de toepassing als u een Voorspellings resource hebt die is toegewezen aan een toepassing. Na de migratie wordt de Voorspellings resource nog steeds toegewezen aan deze toepassingen, ook al hebt u geen toegang meer tot deze apps. Als u de toewijzing wilt verbreken tussen de Voorspellings bron en de toepassing, moet u naar Azure Portal gaan en [de sleutels opnieuw genereren](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Uw toepassingen exporteren.](./media/migrate-authoring-key/migration-export-apps.png)


4. In het venster voor de migratie van regio's wordt u gevraagd om uw toepassingen te migreren naar een Azure-resource in dezelfde regio waarin ze zijn geschreven. LUIS heeft drie ontwerp regio's [en portals](./luis-reference-regions.md#luis-authoring-regions). In het venster worden de regio's weer gegeven waarin uw toepassingen zijn gemaakt. De weer gegeven migratie regio's kunnen afwijken, afhankelijk van de regionale portal die u gebruikt, en de apps die u hebt gemaakt. 

   > [!div class="mx-imgBorder"]
   > ![Migratie van meerdere regio's.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Voor elke regio kiest u voor het maken van een nieuwe LUIS-ontwerp bron of voor het migreren van een bestaande resource met behulp van de knoppen.

   > [!div class="mx-imgBorder"]
   > ![kiezen of u een bestaande ontwerp resource wilt maken](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Geef de volgende informatie op:

   * **Tenant naam**: de Tenant waaraan uw Azure-abonnement is gekoppeld. Deze wordt standaard ingesteld op de Tenant die u momenteel gebruikt. U kunt de tenants wijzigen door dit venster te sluiten en de avatar te selecteren in de rechter bovenhoek van het scherm, waarin uw initialen worden weer gegeven. Klik op **migreren naar Azure** om het venster opnieuw te openen.
   * **Naam** van het Azure-abonnement: het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
   * **Naam van de ontwerp bron**: een aangepaste naam die u kiest. Deze wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's. Als u een nieuwe ontwerp bron maakt, moet u er rekening mee houden dat de resource naam alleen alfanumerieke tekens mag bevatten `-` en mag niet beginnen of eindigen met `-` . Als er andere symbolen zijn opgenomen in de naam, kunnen resources niet worden gemaakt en gemigreerd.
   * **Naam van de Azure-resource groep**: een aangepaste naam voor de resource groep die u kiest in de vervolg keuzelijst. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer. Als u momenteel geen resource groep in uw abonnement hebt, mag u er geen in de LUIS-portal maken. Ga naar [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) om er nog een te maken, gaat u naar Luis om door te gaan met het aanmeldings proces.

6. Nadat u in alle regio's hebt gemigreerd, klikt u op volt ooien. U hebt nu toegang tot uw toepassingen. U kunt door gaan met het ontwerpen en beheren van al uw toepassingen in alle regio's van de portal.

## <a name="migration-notes"></a>Migratie notities

* Vóór de migratie worden mede werkers bekend als _samen_ werkers op het niveau van de Luis-app. Na de migratie wordt de Azure-rol van _Inzender_ gebruikt voor dezelfde functionaliteit op het Azure-resource niveau.
* Als u zich hebt aangemeld voor meer dan één [Luis regionale Portal](./luis-reference-regions.md#luis-authoring-regions), wordt u gevraagd in meerdere regio's tegelijk te migreren.
* Toepassingen worden automatisch met u gemigreerd als u de eigenaar bent van de toepassing. Toepassingen worden niet met u gemigreerd als u een samen werker van de toepassing bent. Mede werkers zullen echter worden gevraagd de apps te exporteren die ze nodig hebben.
* Eigen aren van toepassingen kunnen niet een subset van te migreren apps kiezen en een eigenaar kan niet weten of de mede werkers zijn gemigreerd.
* Bij de migratie worden samen werkers niet automatisch verplaatst of toegevoegd aan de Azure-ontwerp bron. De eigenaar van de app is degene die deze stap na de migratie moet uitvoeren. Voor deze stap zijn [machtigingen vereist voor de Azure-ontwerp bron](./luis-how-to-collaborate.md).
* Nadat inzenders zijn toegewezen aan de Azure-resource, moeten ze worden gemigreerd voordat ze toegang kunnen krijgen tot toepassingen. Anders hebben ze geen toegang tot het maken van de toepassingen.


## <a name="using-apps-after-migration"></a>Apps na migratie gebruiken

Na het migratie proces worden alle LUIS-apps waarvoor u de eigenaar bent, nu toegewezen aan één LUIS-ontwerp bron.
In de lijst **mijn apps** worden de apps weer gegeven die zijn gemigreerd naar de nieuwe ontwerp bron. Voordat u toegang tot uw apps hebt, selecteert u **een andere ontwerp bron kiezen** om het abonnement en de ontwerp bron te selecteren om de apps weer te geven die kunnen worden gemaakt.

> [!div class="mx-imgBorder"]
> ![abonnement selecteren en resource ontwerpen](./media/migrate-authoring-key/select-sub-and-resource.png)


Als u van plan bent om uw apps programmatisch te bewerken, hebt u de sleutel waarden van de ontwerp functie nodig. Deze waarden worden weer gegeven door boven aan het scherm in de LUIS-Portal op **beheren** te klikken en **Azure-resources** te selecteren. Ze zijn ook beschikbaar in de Azure Portal op de pagina **sleutel en eind punten** van de resource. U kunt ook meer ontwerp resources maken en deze toewijzen vanaf dezelfde pagina.

## <a name="adding-contributors-to-authoring-resources"></a>Inzenders toevoegen aan het ontwerpen van resources

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Meer informatie [over het toevoegen van inzenders](luis-how-to-collaborate.md) aan uw ontwerp bron. Inzenders hebben toegang tot alle toepassingen onder die resource.

U kunt mede werkers toevoegen aan de ontwerp bron vanuit het Azure Portal op de pagina **Access Control (IAM)** voor die resource. Zie mede werkers [toevoegen aan uw app](luis-how-to-collaborate.md)voor meer informatie.

> [!Note]
> Als de eigenaar van de LUIS-app is gemigreerd en de samen werker als bijdrager is toegevoegd aan de Azure-resource, heeft de mede werker nog steeds geen toegang tot de app, tenzij deze ook worden gemigreerd.

## <a name="troubleshooting-the-migration-process"></a>Problemen met het migratie proces oplossen

Als u uw Azure-abonnement niet kunt vinden in de vervolg keuzelijst:
* Zorg ervoor dat u een geldig Azure-abonnement hebt dat is gemachtigd om Cognitive Services-resources te maken. Ga naar de [Azure Portal](https://ms.portal.azure.com) en controleer de status van het abonnement. Als u er nog geen hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/cognitive-services/).
* Zorg ervoor dat u zich in de juiste Tenant bevindt die is gekoppeld aan uw geldige abonnement. U kunt in de rechter bovenhoek van het scherm met uw initialen scha kelen tussen tenants om de avatar te selecteren.

  > [!div class="mx-imgBorder"]
  > ![Pagina voor het scha kelen tussen mappen](./media/migrate-authoring-key/switch-directories.png)

Als u een bestaande ontwerp bron hebt, maar deze niet kunt vinden wanneer u de optie **bestaande ontwerp bron gebruiken** selecteert:
* Uw resource is waarschijnlijk gemaakt in een andere regio dan de naam die u wilt migreren in.
* Maak in plaats daarvan een nieuwe resource vanuit de LUIS-Portal.

Als u de optie **nieuwe ontwerp bron maken** selecteert en de migratie mislukt met het fout bericht ' kan de Azure-gegevens van de gebruiker niet ophalen, opnieuw proberen ':
* Uw abonnement heeft mogelijk tien of meer ontwerp resources per regio, per abonnement. Als dat het geval is, kunt u geen nieuwe ontwerp bron maken.
* Migreer door de optie **bestaande ontwerp bron gebruiken** te selecteren en een van de bestaande resources in uw abonnement te selecteren.

## <a name="create-new-support-request"></a>Nieuwe ondersteunings aanvraag maken

Als u problemen ondervindt met de migratie die niet wordt beschreven in de sectie probleem oplossing, [maakt u een ondersteunings onderwerp](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en geeft u de onderstaande informatie op met de volgende velden:

   * **Probleem type**: technisch
   * **Abonnement**: Kies een abonnement in de vervolg keuzelijst
   * **Service**: Zoek en selecteer ' Cognitive Services '
   * **Resource**: Kies een Luis-resource als er al een bestaat. Als dat niet het geval is, selecteert u algemene vraag.

## <a name="next-steps"></a>Volgende stappen

* [Begrippen over het ontwerpen en runtime-sleutels](luis-how-to-azure-subscription.md) bekijken
* Controleren hoe [sleutels worden toegewezen](luis-how-to-azure-subscription.md) en [mede](luis-how-to-collaborate.md) werkers toevoegen