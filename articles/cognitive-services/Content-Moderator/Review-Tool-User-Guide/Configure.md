---
title: Instellingen voor het revisie programma configureren-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik het beoordelings programma om uw team, tags, connectors, werk stromen en referenties voor Content Moderator te configureren of op te halen.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912018"
---
# <a name="configure-the-review-tool"></a>Het beoordelingsprogramma configureren

Het [hulp programma beoordeling](https://contentmoderator.cognitive.microsoft.com) heeft verschillende belang rijke functies die u kunt openen via het menu **instellingen** op het dash board.

![Content Moderator het menu met de instellingen te controleren](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Team en subteams beheren

Op het tabblad **team** kunt u uw team leden en subteamnen &mdash; groepen van gebruikers beheren die kunnen worden gewaarschuwd wanneer bepaalde [mensen beoordelingen](../review-api.md#reviews) worden gestart. U kunt slechts één team gebruiken (dat u maakt wanneer u zich aanmeldt met het hulp programma beoordeling), maar u kunt meerdere subteams maken. De team beheerder kan leden uitnodigen, hun machtigingen instellen en ze toewijzen aan verschillende subteams.

![Team instellingen voor het hulp programma bekijken](images/settings-2-team.png)

Subteams zijn handig voor het maken van escalatie teams of teams die specifiek zijn gericht op het controleren van specifieke inhouds categorieën. U kunt bijvoorbeeld inhoud voor volwassenen naar een afzonderlijk team verzenden voor verdere beoordeling.

In deze sectie wordt uitgelegd hoe u subteamsen maakt en snel Recensies toewijst. U kunt echter [werk stromen](workflows.md) gebruiken om beoordelingen toe te wijzen op basis van specifieke criteria.

### <a name="create-a-subteam"></a>Een subteam maken

Ga naar de sectie **subteams** en klik op **subteam toevoegen**. Voer de naam van uw subteam in het dialoog venster in en klik op **Opslaan**.

![Naam van subteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Team leden uitnodigen

U kunt geen iemand aan een subteam toewijzen als ze nog geen lid zijn van het standaard team. Daarom moet u eerst revisors toevoegen aan het standaard team. Klik op **uitnodigen** op het tabblad **team** .

![Gebruikers uitnodigen](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Team leden toewijzen aan subteam

Klik op de knop **lid toevoegen** om leden van uw standaard team toe te wijzen aan een of meer subteams. U kunt alleen bestaande gebruikers toevoegen aan een subteam. Voor het toevoegen van nieuwe gebruikers die zich niet in het beoordelings programma bevinden, kunt u ze uitnodigen met behulp van de knop ' uitnodigen ' op de pagina team instellingen.

![Leden van een subteam toewijzen](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Beoordelingen toewijzen aan subteams

Zodra u uw subteams en toegewezen leden hebt gemaakt, kunt u beginnen met het toewijzen van inhouds [beoordelingen](../review-api.md#reviews) aan die subteams. Dit doet u via het tabblad **controleren** van de site.
Als u inhoud aan een subteam wilt toewijzen, klikt u op het beletsel teken in de rechter bovenhoek, selecteert **u verplaatsen naar** en selecteert u een subteam.

![Afbeeldings beoordeling aan subteam toewijzen](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Scha kelen tussen subteams

Als u lid bent van meer dan één subteam, kunt u scha kelen tussen deze subteams om te wijzigen welke inhouds Beoordelingen voor u worden weer gegeven. Op het tabblad **controleren** selecteert u de vervolg keuzelijst met de naam **standaard** en selecteert u **subteam kiezen**. U kunt de inhouds Beoordelingen voor verschillende subteams weer geven, maar alleen degenen waarvan u lid bent.

![Scha kelen tussen subteams](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

Op het tabblad **labels** kunt u naast de twee standaard markeringen &mdash; **isadult** (**a**) en **isracy** (**r**) aangepaste toezicht Tags definiëren. Wanneer u een aangepaste tag maakt, wordt deze beschikbaar in beoordelingen naast de standaard tags. U kunt wijzigen welke tags worden weer gegeven in beoordelingen door de instellingen voor zicht baarheid van hun voor beeld te scha kelen.

![De weer gave Tags, inclusief de selectie vakjes ' is zichtbaar '](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Aangepaste labels maken

Als u een nieuwe tag wilt maken, moet u een korte code, naam en beschrijving in de desbetreffende velden invoeren.

- **Korte code**: Voer een code van twee letters in voor de tag. Voor beeld: **CB**
- **Naam**: Voer een korte en beschrijvende label naam in in kleine letters zonder spaties. Voor beeld: **isbullying**.
- **Beschrijving**: (optioneel) Voer een beschrijving in van het type inhoud dat door de tag wordt doel. Voor beeld: **afbeeldingen of instanties van Cyber Bullying**.

Klik op **toevoegen** om een tag toe te voegen en klik op **Opslaan** wanneer u klaar bent met het maken van tags.

![Hulp programma voor het maken van een nieuw label dialoog venster](images/settings-3-tags.png)

### <a name="delete-tags"></a>Tags verwijderen

U kunt aangepaste tags verwijderen door het prullenbak pictogram naast hun vermeldingen in de lijst Tags te selecteren, maar u kunt de standaard tags niet verwijderen.

## <a name="connectors"></a>Connectors

Op het tabblad **connectors** kunt u uw connectors beheren. Dit zijn service-specifieke invoeg toepassingen die inhoud op verschillende manieren kunnen verwerken als onderdeel van [werk stromen](../review-api.md#workflows)voor inhoud.

De standaard connector wanneer u een werk stroom maakt, is de Content Moderator-connector, waarmee inhoud als **volwassene** of **ongepaste** kan worden gemarkeerd, de woorden kunnen worden gevonden, enzovoort. U kunt echter andere connectors gebruiken die hier worden weer gegeven, mits u referenties voor hun respectieve services hebt (u moet bijvoorbeeld een [Face](../../face/overview.md) -abonnements sleutel ophalen).

Het [hulp programma voor beoordeling](./human-in-the-loop.md) bevat de volgende connectors:

- Emotion
- Face
- PhotoDNA-Cloud service
- Text Analytics

### <a name="add-a-connector"></a>Een connector toevoegen

Als u een connector wilt toevoegen (en deze beschikbaar wilt maken voor gebruik in inhouds [werk stromen](../review-api.md#workflows)), selecteert u de juiste knop **verbinden** . Voer in het volgende dialoog venster uw abonnements sleutel voor die service in. Wanneer u klaar bent, wordt de nieuwe connector boven aan de pagina weer gegeven.

![Instellingen voor Content Moderator connectors](images/settings-4-connectors.png)

## <a name="workflows"></a>Werkstromen

Op het tabblad **werk stromen** kunt u uw [werk stromen](../review-api.md#workflows)beheren. Werk stromen zijn Cloud filters voor inhoud en ze werken met connectors om inhoud op verschillende manieren te sorteren en de juiste acties uit te voeren. Hier kunt u uw werk stromen definiëren, bewerken en testen. Zie [werk stromen definiëren en gebruiken](Workflows.md) voor richt lijnen voor informatie over hoe u dit doet.

![Werk stroom instellingen Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Referenties

Het tabblad **referenties** biedt snelle toegang tot uw content moderator-abonnements sleutel, die u nodig hebt om toegang te krijgen tot een van de toezicht services van een rest-aanroep of client-SDK.

![Content Moderator referenties](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Externe referenties voor werk stromen gebruiken

Het [hulp programma beoordeling](https://contentmoderator.cognitive.microsoft.com) genereert een gratis proef versie voor Azure content moderator Services wanneer u zich aanmeldt, maar u kunt deze ook configureren voor het gebruik van een bestaande sleutel van uw Azure-account. Dit wordt aanbevolen voor grootschalige scenario's, aangezien de gratis proef sleutels strikte gebruiks limieten ([prijzen en limieten](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)) hebben.

Als u een [Content moderator resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) hebt gemaakt in azure, navigeert u deze in de Azure Portal en selecteert u de Blade **sleutels** . Kopieer een van de sleutels.

![Content Moderator sleutels in de Azure Portal](images/credentials-azure-portal-keys.PNG)

Ga op het tabblad controle **van het** [hulp programma](https://contentmoderator.cognitive.microsoft.com)naar het deel venster **werk stroom instellingen** , selecteer **bewerken** en plak uw sleutel in het veld **OCP-APIM-Subscription-Key** . Nu gebruiken werk stromen die de toezicht-Api's aanroepen, uw Azure-referentie.

> [!NOTE]
> De andere twee velden in het deel venster **werk stroom instellingen** zijn voor aangepaste termen en lijsten met installatie kopieën. Raadpleeg de hand leidingen voor [aangepaste voor waarden](../try-terms-list-api.md) of [aangepaste afbeeldingen](../try-image-list-api.md) voor meer informatie.

### <a name="use-your-azure-account-with-the-review-apis"></a>Uw Azure-account gebruiken met de controle-Api's

Als u uw Azure-sleutel wilt gebruiken met de controle-Api's, moet u de resource-ID ophalen. Ga naar uw Content Moderator-resource in de Azure Portal en selecteer de Blade **Eigenschappen** . Kopieer de waarde van de resource-ID en plak deze in het veld **white list resource-id ('s)** op het tabblad **referenties** van het controle programma.

![Content Moderator Resource-ID in de Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Als u uw abonnements sleutel op beide locaties hebt ingevoerd, wordt de proef versie die wordt meegeleverd met het account van uw beoordelings programma niet gebruikt, maar blijft deze beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Volg de Quick Start van het [hulp programma](../quick-start.md) om te beginnen met het hulp programma beoordeling in scenario's voor inhouds toezicht.