---
title: Migreren naar een Azure-resource bewerkings sleutel
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u Language Understanding (LUIS) authoring-verificatie van een e-mail account naar een Azure-resource migreert.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 455c7d66748740ae6e2cc11c6a44bbf30c1cbced
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018834"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migreren naar een Azure-resource bewerkings sleutel

Language Understanding-ontwerp verificatie (LUIS) is gewijzigd van een e-mail account in een Azure-resource. Hoewel het momenteel niet vereist is, wordt het overschakelen naar een Azure-resource in de toekomst afgedwongen.


## <a name="what-is-migration"></a>Wat is migratie?

Migratie is het proces van het wijzigen van de ontwerp verificatie van een e-mail account in een Azure-resource. Uw account wordt gekoppeld aan een Azure-abonnement en een Azure-ontwerp bron nadat u de migratie hebt gemigreerd. *Alle LUIS-gebruikers (eigen aren of mede werkers) zullen uiteindelijk moeten worden gemigreerd.*

Migratie moet worden uitgevoerd vanuit de LUIS-Portal. Als u de ontwerp sleutels maakt met behulp van de LUIS CLI, moet u bijvoorbeeld het migratie proces in de LUIS-Portal volt ooien. U kunt na de migratie nog steeds coauteurs hebben op uw toepassingen, maar deze worden toegevoegd op het Azure-resource niveau in plaats van op toepassings niveau.

> [!Note]
> Vóór de migratie worden mede werkers bekend als _samen_ werkers op het niveau van de Luis-app. Na de migratie wordt de Azure-rol van _Inzender_ gebruikt voor dezelfde functionaliteit op het Azure-resource niveau.

## <a name="note-before-you-migrate"></a>Opmerking Voordat u migreert

* U moet uw ontwerp ervaring migreren op basis van **november, 2, 2020**. 
* Migratie is een eenrichtings proces. U kunt niet teruggaan nadat u hebt gemigreerd.
* Toepassingen worden automatisch met u gemigreerd als u de eigenaar bent van de toepassing.
* De eigenaar kan geen subset van te migreren apps kiezen en het proces kan niet onomkeerbaar zijn.
* Toepassingen worden verwijderd van de zijde van de mede werker nadat de eigenaar is gemigreerd.
* Eigen aren worden gevraagd e-mail berichten te verzenden naar mede werkers om hen te informeren over de migratie.
* Toepassingen worden niet met u gemigreerd als u een samen werker van de toepassing bent.
* Het is niet mogelijk dat een eigenaar weet dat mede werkers zijn gemigreerd.
* Bij de migratie worden niet automatisch samen werkers verzameld en verplaatst of toegevoegd aan de Azure-ontwerp bron. De eigenaar van de app is degene die deze stap na de migratie moet uitvoeren. Voor deze stap zijn [machtigingen vereist voor de Azure-ontwerp bron](./luis-how-to-collaborate.md).
* Nadat deel nemers zijn toegewezen aan de Azure-resource, moeten ze migreren naar Access-toepassingen. Anders hebben ze geen toegang tot het maken van de toepassingen.
* Een gemigreerde gebruiker kan niet worden toegevoegd als een samen werker van de toepassing.
* Als u de Voorspellings sleutels hebt die zijn toegewezen aan toepassingen die eigendom zijn van een andere gebruiker, wordt de migratie voor zowel de eigenaar als de mede werkers geblokkeerd. Zie de aanbevelingen verderop in dit artikel.

> [!Note]
> Als u een Voorspellings runtime-resource moet maken, is er [een afzonderlijk proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) om het te maken.

## <a name="migration-prerequisites"></a>Migratie vereisten

* U moet zijn gekoppeld aan een geldig Azure-abonnement. Vraag uw Tenant beheerder om u toe te voegen aan het abonnement of Meld u aan [voor een gratis account](https://azure.microsoft.com/free/cognitive-services).
* U moet een LUIS Azure-ontwerp bron maken via de LUIS-portal of van de Azure Portal. Het maken van een ontwerp bron vanuit de LUIS-Portal maakt deel uit van de migratie stroom die in de volgende sectie wordt besproken.
* Als u een samen werker van toepassingen bent, worden de toepassingen niet automatisch gemigreerd. U wordt aangeraden een back-up te maken van deze toepassingen door deze te exporteren of door de [API export](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)te gebruiken. U kunt de app na de migratie weer in LUIS importeren. Het import proces maakt een nieuwe app met een nieuwe app-ID waarvoor u de eigenaar bent.
* Als u de eigenaar van de toepassing bent, hoeft u uw apps niet te exporteren omdat deze automatisch worden gemigreerd. We raden u aan elke lijst van de samen werker van de app op te slaan. Een e-mail sjabloon met deze lijst wordt optioneel als onderdeel van het migratie proces aangeboden.


|Portal|Doel|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Voorspellings-en ontwerp resources maken.<br> Mede werkers aan resources toewijzen.|
|[LUIS](https://www.luis.ai)| Migreren naar nieuwe ontwerp resources.<br> Nieuwe ontwerp resources maken in de migratie stroom.<br> U kunt voor spellingen en het ontwerpen van resources voor apps toewijzen of verwijderen **Manage** via de  >  pagina **Azure-resources** beheren. <br> Verplaats toepassingen van de ene ontwerp bron naar een andere.  |

> [!Note]
> Het is gratis om uw LUIS-app te ontwerpen, zoals wordt aangegeven door de laag F0. Meer informatie [over prijs categorieën](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Migratiestappen

1. In de LUIS-Portal waaraan u werkt, kunt u het migratie proces starten vanuit het **Azure** -pictogram op de bovenste werk balk.

   > [!div class="mx-imgBorder"]
   > ![Pictogram migratie](./media/migrate-authoring-key/migration-button.png)

2. In het pop-upvenster migratie kunt u de migratie voortzetten of later migreren. Selecteer **nu migreren**.

   > [!div class="mx-imgBorder"]
   > ![Eerste pop-upvenster in het migratie proces, waar u nu migreren selecteert](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Als uw apps deel nemers hebben, wordt u gevraagd om hen een e-mail te sturen waarmee ze over de migratie kunnen weten. Dit is een optionele taak.

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

4. Als u voor elke toepassing een samen werker bent, wordt u gevraagd een kopie van de apps te exporteren door deze optie te selecteren tijdens de migratie stroom. Dit is een optionele taak.

   Als u de optie selecteert, wordt de volgende pagina weer gegeven. Selecteer de Download knoppen aan de linkerkant om de gewenste apps te exporteren. U kunt deze apps weer importeren nadat u bent gemigreerd, omdat ze niet automatisch met u worden gemigreerd.

   > [!div class="mx-imgBorder"]
   > ![Vragen om het exporteren van uw toepassingen.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. U kunt ervoor kiezen om een nieuwe LUIS te maken of te migreren naar een bestaande ontwerp bron als u er al een hebt gemaakt in Azure. Kies de gewenste optie door een van de volgende knoppen te selecteren.

   > [!div class="mx-imgBorder"]
   > ![Knoppen voor het maken van een nieuwe ontwerp bron en het gebruik van een bestaande ontwerp bron](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Nieuwe ontwerp bron maken van LUIS om te migreren

Als u een nieuwe ontwerp bron wilt maken, selecteert u **nieuwe ontwerp bron maken** en geeft u de volgende informatie op in het volgende venster. Selecteer vervolgens **Done**.

> [!div class="mx-imgBorder"]
> ![Venster voor het maken van een ontwerp bron](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Tenant naam**: de Tenant waaraan uw Azure-abonnement is gekoppeld. Dit wordt standaard ingesteld voor de Tenant die u momenteel gebruikt. U kunt tenants overschakelen door de meest rechtse avatar te selecteren, die uw initialen bevat.
* **Resource naam**: een aangepaste naam die u kiest. Deze wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's.
* **Abonnements naam**: het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
* **Naam van de Azure-resource groep**: een aangepaste naam voor de resource groep die u kiest in de vervolg keuzelijst. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.

Houd er rekening mee dat u per abonnement 10 gratis ontwerp resources per regio kunt maken. Als uw abonnement meer dan 10 ontwerp bronnen heeft in dezelfde regio, kunt u geen nieuwe maken.

Wanneer de ontwerp bron is gemaakt, wordt het bericht weer gegeven. Selecteer **sluiten** om het pop-upvenster te sluiten.

  > [!div class="mx-imgBorder"]
  > ![Bericht dat aangeeft dat uw ontwerp bron is gemaakt](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Bestaande ontwerp Resource gebruiken om te migreren

Als uw abonnement al is gekoppeld aan een Azure-resource met LUIS-ontwerp, of als u een resource hebt gemaakt op basis van de Azure Portal en u deze wilt migreren in plaats van een nieuwe te maken, selecteert u **bestaande ontwerp Resource gebruiken**. Geef de volgende informatie op in het volgende venster en selecteer vervolgens **gereed**.

> [!div class="mx-imgBorder"]
>![Venster voor het wijzigen van een bestaande ontwerp bron](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Tenant naam**: de Tenant waaraan uw Azure-abonnement is gekoppeld. Dit wordt standaard ingesteld voor de Tenant die u momenteel gebruikt.
* **Abonnements naam**: het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
* **Resource naam**: de ontwerp resource waarnaar u wilt migreren.

> [!Note]
> Als u uw bewerkings bron niet in de vervolg keuzelijst ziet, zorg er dan voor dat u deze hebt gemaakt op de juiste locatie op basis van de LUIS-Portal waar u bent aangemeld. Zorg er ook voor dat de gegevens die u hebt gemaakt, een ontwerp bron zijn en geen Voorspellings resource.


Valideer de naam van uw ontwerp bron en selecteer de knop **migreren** .

> [!div class="mx-imgBorder"]
> ![Venster voor het kiezen van een bewerkings resource, met de knop migreren nu beschikbaar](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Er wordt een bericht weer gegeven. Selecteer **sluiten** om het pop-upvenster te sluiten.

> [!div class="mx-imgBorder"]
> ![Bericht met de mede deling dat uw ontwerp bron is gemigreerd](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Apps na migratie gebruiken

Na het migratie proces worden alle LUIS-apps waarvoor u de eigenaar bent, nu toegewezen aan één LUIS-ontwerp bron.

In de lijst **mijn apps** worden de apps weer gegeven die zijn gemigreerd naar de nieuwe ontwerp bron. Voordat u toegang krijgt tot uw apps, selecteert u het abonnement en de LUIS-ontwerp bron om de apps te zien die u kunt ontwerpen.

 > [!div class="mx-imgBorder"]
 > ![Vakken voor het abonnement en de ontwerp bron](./media/create-app-in-portal-select-subscription-luis-resource.png)

U hoeft niet de sleutel van de ontwerp bron te weten om uw apps in de LUIS-portal te kunnen bewerken.

Als u van plan bent om uw apps programmatisch te bewerken, hebt u de sleutel waarden van de ontwerp functie nodig. Deze waarden worden weer gegeven op **Manage** de  >  pagina **Azure-resources** beheren in de Luis-Portal. Ze zijn ook beschikbaar in de Azure Portal op de pagina **sleutels** van de resource. U kunt ook meer ontwerp resources maken en deze toewijzen vanaf dezelfde pagina.

 > [!div class="mx-imgBorder"]
 > ![Pagina voor het beheren van ontwerp resources](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Inzenders toevoegen aan het ontwerpen van resources

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Meer informatie [over het toevoegen van inzenders](luis-how-to-collaborate.md) aan uw ontwerp bron. Inzenders hebben toegang tot alle toepassingen onder die resource.

U kunt mede werkers toevoegen aan de ontwerp bron vanuit het Azure Portal op de pagina **Access Control (IAM)** voor die resource. Zie mede werkers [toevoegen aan uw app](luis-how-to-collaborate.md)voor meer informatie.

> [!Note]
> Als de eigenaar van de LUIS-app is gemigreerd en de samen werker als bijdrager is toegevoegd aan de Azure-resource, heeft de mede werker nog steeds geen toegang tot de app, tenzij deze ook worden gemigreerd.

## <a name="luis-portal-migration-reminders"></a>Migratie herinneringen voor LUIS Portal

De [Luis-Portal](https://www.luis.ai) biedt het migratie proces.

U wordt gevraagd om te migreren als aan beide voor waarden wordt voldaan:
* U hebt apps in het e-mail verificatie systeem voor het ontwerpen.
* U bent de eigenaar van de app.

U wordt wekelijks gevraagd uw apps te migreren. U kunt dit venster sluiten zonder te migreren. Als u vóór de volgende geplande periode wilt migreren, kunt u beginnen met het migratie proces vanuit het **Azure** -pictogram op de bovenste werk balk van de Luis-Portal.

## <a name="prediction-resources-blocking-migration"></a>Migratie van voor Spellings bronnen blok keren
Uw migratie heeft een negatieve invloed op de runtime van elke toepassing. Wanneer u een migratie uitvoert, worden alle deel nemers uit uw apps verwijderd en wordt u verwijderd als samen werker van andere apps. Dit proces houdt in dat de sleutels die een mede werker toewijst ook worden verwijderd, waardoor uw toepassing kan worden verbroken als deze in productie is. Dit is de reden dat we de migratie blok keren totdat u hand matig deel nemers of sleutels hebt verwijderd die aan hen zijn toegewezen.

Migratie wordt geblokkeerd als aan een van deze voor waarden wordt voldaan:

* U hebt voorspellings-en runtime-resources toegewezen in apps waarvan u geen eigenaar bent.
* U hebt andere gebruikers de voor spelling-en runtime-resources toegewezen aan apps waarvan u eigenaar bent.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Aanbevolen stappen als u de eigenaar van de app bent
Als u eigenaar bent van sommige toepassingen en er samen werkers zijn toegewezen aan een Voorspellings-of runtime sleutel aan deze toepassingen, wordt er een fout bericht weer gegeven wanneer u migreert. De fout geeft een lijst van de toepassings-Id's waarvoor Voorspellings sleutels zijn toegewezen aan andere gebruikers.

U wordt aangeraden dat u:
* Deel mede werkers over de migratie.
* Verwijder alle deel nemers uit de toepassingen die in de fout worden weer gegeven.
* Het migratie proces wordt uitgevoerd. dit moet slagen als u samen werkers hand matig verwijdert.
* Deel nemers als mede werkers aan uw nieuwe ontwerp bron toewijzen. Samen werkers worden de Voorspellings bronnen gemigreerd en opnieuw toegewezen aan de toepassingen. Houd er rekening mee dat dit tot gevolg heeft dat de toepassing tijdelijk wordt onderbroken totdat de Voorspellings bronnen opnieuw worden toegewezen.

Hier vindt u een mogelijke oplossing. Vóór de migratie van de eigenaar kunnen samen werkers app-eigen aren toevoegen als bijdrager aan hun Azure-abonnementen vanuit de Azure Portal. Met deze stap wordt de eigenaar toegang verleend tot de runtime-Voorspellings resource. Als de eigenaar wordt gemigreerd met behulp van het nieuwe abonnement waaraan ze zijn toegevoegd (en die wordt gevonden onder een nieuwe Tenant), wordt met deze stap niet alleen de deblokkeren van het migratie proces voor zowel de samen werker als de eigenaar van de app. Het biedt ook een soepele migratie van apps, waarbij de Voorspellings sleutel nog steeds is toegewezen aan de apps.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Aanbevolen stappen als u een samen werker van een app bent
Als u samen met toepassingen werkt en u een voor spelling/runtime-sleutel aan deze toepassingen hebt toegewezen, wordt er een fout bericht weer gegeven wanneer u migreert. De fout geeft een lijst van de toepassings-Id's en sleutel paden die de migratie blok keren.

U wordt aangeraden dat u:
* Toepassingen exporteren als back-up. Dit is een optionele stap in het migratie proces.
* Toewijzing van de Voorspellings resources op de **Manage**  >  pagina **Azure-resources** beheren.
* Het migratie proces wordt uitgevoerd.
* Importeer toepassingen na de migratie.
* De Voorspellings sleutels worden opnieuw toegewezen aan uw toepassingen **Manage** via de  >  pagina **Azure-resources** beheren.

> [!Note]
> Wanneer u uw toepassingen importeert nadat u bent gemigreerd, hebben ze verschillende app-Id's. Ze zijn ook verschillend van de productie. U bent nu eigenaar van deze toepassingen.

## <a name="troubleshooting-the-migration-process"></a>Problemen met het migratie proces oplossen

Wanneer u uw Azure-abonnement in de vervolg keuzelijst wilt migreren, maar niet kunt vinden:
* Zorg ervoor dat u een geldig Azure-abonnement hebt dat is gemachtigd om Cognitive Services-resources te maken. Ga naar de [Azure Portal](https://ms.portal.azure.com) en controleer de status van het abonnement. Als u er nog geen hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/cognitive-services/).
* Zorg ervoor dat u zich in de juiste Tenant bevindt die is gekoppeld aan uw geldige abonnement. U kunt tenants van de avatar naar links van uw initialen op deze werk balk overschakelen: ![ werk balk waar u tenants kunt overschakelen](./media/migrate-authoring-key/switch-user-tenant-2.png)

Als u een bestaande ontwerp bron hebt, maar deze niet kunt vinden wanneer u de optie **bestaande ontwerp bron gebruiken** selecteert:
* Uw resource is waarschijnlijk gemaakt op een andere locatie dan de portal waar u bent aangemeld. Controleer de [Luis-ontwerp regio's en-portals](./luis-reference-regions.md#luis-authoring-regions).
* Maak in plaats daarvan een nieuwe resource vanuit de LUIS-Portal.

Als u de optie **nieuwe ontwerp bron maken** selecteert en de migratie mislukt met het fout bericht ' kan de Azure-gegevens van de gebruiker niet ophalen, opnieuw proberen ':
* Uw abonnement heeft mogelijk tien of meer ontwerp resources per regio, per abonnement. Als dat het geval is, kunt u geen nieuwe ontwerp bron maken.
* Migreer door de optie **bestaande ontwerp bron gebruiken** te selecteren en een van de bestaande resources in uw abonnement te selecteren.

Als u de volgende fout ziet, controleert [u de aanbevolen stappen als u de eigenaar van de app bent](#recommended-steps-if-youre-the-owner-of-the-app).
![Er is een fout opgetreden bij het weer geven van de migratie voor eigen aars](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Als u de volgende fout ziet, raadpleegt u de [Aanbevolen stappen als u een samen werker van een app bent](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Er is een fout opgetreden bij het weer geven van de migratie voor samen werkers](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Volgende stappen

* Bekijk [concepten over ontwerpen en runtime-sleutels](luis-how-to-azure-subscription.md).
* Lees hoe u [sleutels kunt toewijzen](luis-how-to-azure-subscription.md) en mede werkers kunt [toevoegen](luis-how-to-collaborate.md).
