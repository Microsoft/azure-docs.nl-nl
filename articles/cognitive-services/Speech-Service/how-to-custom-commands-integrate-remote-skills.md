---
title: 'Procedure: een toepassing met aangepaste opdrachten exporteren als een externe vaardigheids service'
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u een toepassing met aangepaste opdrachten kunt exporteren als een vaardigheid
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025871"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Toepassing voor het exporteren van aangepaste opdrachten als externe vaardigheid

In dit artikel wordt beschreven hoe u een toepassing voor aangepaste opdrachten exporteert als externe vaardigheid.

## <a name="prerequisites"></a>Vereisten
> [!div class="checklist"]
> * [Kennis van de vaardigheid van het bot-Framework](/azure/bot-service/skills-conceptual)
> * [Kennis van het kwalificatie manifest](https://aka.ms/speech/cc-skill-manifest)
> * [Een vaardigheid aanroepen vanuit een bot-raam werk](/azure/bot-service/skills-about-skill-consumers)
> * Een bestaande toepassing voor aangepaste opdrachten. Als u geen aangepaste opdrachten toepassing hebt, kunt u proberen om- [Quick Start: een spraak assistent maken met aangepaste opdrachten](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Aangepaste opdrachten als externe vaardig heden
* Bot Framework-vaardig heden zijn herbruikbare functie voor het maken van conversaties op basis van conversaties-blokken voor gespreks gebruik. Hiermee kunt u binnen enkele minuten uitgebreide functionaliteit toevoegen aan een bot. Ga voor meer informatie naar [bot Framework-vaardig heden](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Een toepassing voor aangepaste opdrachten kan worden geëxporteerd als een kwalificatie. Deze vaardigheid kan vervolgens via het externe vaardigheden protocol worden aangeroepen vanuit een bot Framework-bot.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Een toepassing zo configureren dat deze wordt weer gegeven als externe vaardigheid

### <a name="application-level-settings"></a>Instellingen op toepassings niveau
1. Selecteer in het linkerdeel venster **instellingen**  >  **externe vaardig heden**.
1. Stel **externe vaardig heden** in op de schakel optie ingeschakeld.

### <a name="authentication-to-skills"></a>Verificatie voor vaardig heden
1. Als u verificatie wilt inschakelen, voegt u micro soft-toepassings-Id's toe van het bot-Framework-bots dat u wilt configureren om de toepassing voor aangepaste opdrachten aan te roepen.
      > [!div class="mx-imgBorder"]
      > ![Een MSA-id toevoegen aan de vaardigheid](media/custom-commands/skill-add-msa-id.png)

1. Als er ten minste één item is toegevoegd aan de lijst, wordt verificatie ingeschakeld op de toepassing en kan alleen het toegestane bots de toepassing aanroepen.
> [!TIP]
>  Als u verificatie wilt uitschakelen, verwijdert u alle micro soft-toepassings-Id's uit de lijst met toegestane apps. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Opdrachten die kunnen worden weer gegeven als vaardig heden inschakelen/uitschakelen

U hebt de mogelijkheid om te kiezen welke opdrachten u wilt exporteren via externe vaardig heden.

1. Als u een opdracht wilt weer geven over vaardig heden, selecteert u **een nieuwe opdracht inschakelen** onder de **opdrachten voor het inschakelen van vaardig heden**.
1. Selecteer in de vervolg keuzelijst de opdracht die u wilt toevoegen.
1. Selecteer **Opslaan**.

### <a name="configure-triggering-utterances-for-commands"></a>Triggering uitingen configureren voor opdrachten
Aangepaste opdrachten gebruiken de voor beelden van de zinnen die zijn geconfigureerd voor de opdrachten om de vaardig heden voor het activeren van uitingen te genereren. Deze **triggering uitingen** wordt gebruikt voor het genereren van het [**Kwalificatie manifest**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)voor de sectie **dispatcher** .

Als auteur wilt u misschien bepalen welke van uw **voorbeeld zinnen** worden gebruikt voor het genereren van de trigger uitingen voor vaardig heden.
1. Standaard worden alle **activerings voorbeelden** van een opdracht opgenomen in het manifest bestand.
1. Als u één voor beeld expliciet wilt verwijderen, selecteert u in de sectie **ingeschakelde opdrachten voor het gedeelte vaardig heden** het pictogram **bewerken** .
    > [!div class="mx-imgBorder"]
    > ![Een ingeschakelde opdracht voor vaardigheid bewerken](media/custom-commands/skill-edit-enabled-command.png)

1. Klik vervolgens op de voorbeeld zinnen die u wilt weglaten, met de **rechter muisknop op**  >  **voor beeld van een zin uitschakelen**.
    > [!div class="mx-imgBorder"]
    > ![Voor beelden uitschakelen](media/custom-commands/skill-disable-example-sentences.png)

1. Selecteer **Opslaan**.
1. U ziet dat u in dit venster geen nieuw voor beeld kunt toevoegen. Als dit het geval is, gaat u naar de sectie instellingen afsluiten en selecteert u de relevante opdracht uit de accordeon van de **opdrachten** . Op dit moment kunt u de nieuwe vermelding in de sectie **voor beelden van zinnen** toevoegen. Deze wijziging wordt automatisch weer gegeven in de waarde voor de externe vaardigheids instellingen voor de opdracht.

> [!IMPORTANT]
> Als uw bestaande voorbeeld zinnen verwijzingen bevatten naar **String > Catalog** -gegevens type, worden deze zinnen automatisch wegge laten uit de uitingen lijst met vaardig heden. 

## <a name="download-skill-manifest"></a>Vaardigheids manifest downloaden
1. Nadat u uw toepassing hebt **gepubliceerd** , kunt u het bestand met het vaardigheids manifest downloaden.
1. Gebruik het vaardigheids manifest om uw bot Framework-consumenten bot te configureren om in te bellen bij de aangepaste-opdracht-vaardigheid.
> [!IMPORTANT]
> U moet uw toepassing voor aangepaste opdrachten **publiceren** om het kwalificatie manifest te kunnen downloaden. </br>
> Als u **wijzigingen** hebt aangebracht in de toepassing, moet u ook de toepassing opnieuw publiceren, zodat de meest recente wijzigingen worden weer gegeven in het manifest bestand.

> [!NOTE]
> Als u problemen ondervindt bij het publiceren van de toepassing en de fout wordt doorgestuurd naar vaardig heden die uitingen activeren, controleert u de configuratie van de **ingeschakelde opdrachten voor vaardig heden** opnieuw. Elk van de weer gegeven opdrachten moet ten minste één geldige trigger voor utterance hebben.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een opdracht van de client bijwerken](./how-to-custom-commands-update-command-from-client.md)
