---
title: Ondersteuning krijgen voor avere vFXT voor Azure
description: Meer informatie over het oplossen van problemen die zich kunnen voordoen tijdens het implementeren of gebruiken van AVERE vFXT voor Azure door het maken van een ondersteunings ticket via de Azure Portal.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008426"
---
# <a name="get-help-with-your-system"></a>Hulp vragen bij uw systeem

Voor hulp bij uw avere vFXT voor Azure-systeem zijn de volgende manieren om ondersteuning te krijgen:

* **Avere vFXT-probleem** : gebruik de Azure Portal om een ondersteunings ticket voor uw avere-vFXT te openen, zoals [hieronder](#open-a-support-ticket-for-your-avere-vfxt)wordt beschreven.
* **Quotum** : als u een quotum wilt hebben, moet u [een quotum toename aanvragen](#request-a-quota-increase)
* **Documentatie en voor beelden** : als u problemen ondervindt met deze documentatie of voor beelden, schuift u naar de onderkant van de pagina met het probleem en gebruikt u de sectie **feedback** om naar bestaande problemen te zoeken en een nieuwe te bestand, indien nodig.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Open een ondersteunings ticket voor uw avere-vFXT

Als u problemen ondervindt tijdens het implementeren of gebruiken van AVERE vFXT, vraagt u hulp via de Azure Portal.

Volg deze stappen om ervoor te zorgen dat uw ondersteunings ticket is gelabeld met een resource uit uw cluster. Als u het ticket labelt, kunnen we dit routeren naar de juiste ondersteunings resource.

1. Van [https://portal.azure.com](https://portal.azure.com) selecteert u **resource groepen**. Blader naar de resource groep die het vFXT-cluster bevat waarop het probleem zich heeft voorgedaan en klik op een van de virtuele avere-cluster machines.

    ![scherm afbeelding van het deel venster Overzicht van Azure Portal-resource groep met een bepaalde VM omcirkeld](media/avere-vfxt-ticket-vm.png)

1. Schuif op de pagina VM omlaag naar de onderkant van het linkerdeel venster en klik op **nieuwe ondersteunings aanvraag**.

    ![Scherm afbeelding van de Azure Portal VM-pagina voor de virtuele machine van de vorige scherm afbeelding. Het menu links wordt naar beneden geschoven en ' nieuwe ondersteunings aanvraag ' wordt omcirkeld.](media/avere-vfxt-ticket-request.png)

1. Kies op de eerste pagina van de ondersteunings aanvraag het probleem type en controleer of het juiste abonnement is geselecteerd.

   Klik onder **service** op **alle services** en zoek onder **opslag** om **avere vFXT** te kiezen.

   Voeg een korte samen vatting toe en selecteer het probleem type.

    ![scherm opname van een nieuw venster voor ondersteunings aanvraag in de Azure Portal. Het tabblad basis beginselen is geselecteerd. Scherm items zijn onder meer probleem type, abonnement, service, samen vatting en probleem type.](media/ticket-basics.png)

   Klik op **Volgende** om verder te gaan.

1. De tweede pagina van het formulier ondersteuning bevat suggesties voor het oplossen van het probleem op basis van de beschrijving van uw samen vatting. Klik op de knop **volgende** onderaan als u nog steeds een ondersteunings ticket moet maken.

   ![scherm afbeelding van het venster nieuwe ondersteunings aanvraag met het tabblad oplossingen geselecteerd. Een tekst veld in het midden bevat de titel aanbevolen oplossing en legt mogelijke oplossingen voor.](media/ticket-solutions.png)

1. Geef op de derde pagina Details op. Dit omvat informatie over uw cluster, het tijdstip waarop het probleem zich voordeed, de ernst en hoe u contact met u opneemt. Vul de informatie in en klik onderaan op de knop **volgende** .

   ![scherm afbeelding van het venster nieuwe ondersteunings aanvraag met het tabblad Details geselecteerd. Informatie velden zijn ingedeeld in de categorieën ' probleem Details ', ' ondersteunings methode ' en ' contact gegevens '.](media/ticket-details.png)

1. Controleer de informatie op de laatste pagina en klik op **maken**. Er wordt een bevestigings-en ticket nummer verzonden naar uw e-mail adres en een ondersteunings medewerker neemt contact met u op.

## <a name="request-a-quota-increase"></a>Een quotum verhoging aanvragen

Lees [quotum voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) voor meer informatie over de onderdelen die nodig zijn voor het implementeren van de avere VFXT voor Azure. U kunt [een quotum verhoging aanvragen](../azure-portal/supportability/resource-manager-core-quotas-request.md) van de Azure Portal.