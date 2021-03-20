---
title: Peer-ASN koppelen aan Azure-abonnement met de portal
titleSuffix: Azure
description: Peer-ASN koppelen aan Azure-abonnement met de portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540265"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Peer-ASN koppelen aan Azure-abonnement met de portal

Als Internet serviceprovider of Internet Exchange-provider moet u eerst uw ASN koppelen aan een Azure-abonnement met behulp van de onderstaande stappen voordat u een peering-aanvraag indient.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn maken om uw ASN te koppelen aan een Azure-abonnement

### <a name="sign-in-to-the-portal"></a>Aanmelden bij de portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registreren voor de resource provider voor peering
Registreer u voor de resource provider voor de peering in uw abonnement door de volgende stappen uit te voeren. Als u deze niet uitvoert, zijn Azure-resources die vereist zijn voor het instellen van peering, niet toegankelijk.

1. Klik op **abonnementen** in de linkerbovenhoek van de portal. Als u deze niet ziet, klikt u op **meer services** en zoekt u deze.

    > [!div class="mx-imgBorder"]
    > ![Abonnementen openen](./media/rp-subscriptions-open.png)

1. Klik op het abonnement dat u wilt gebruiken voor peering.

    > [!div class="mx-imgBorder"]
    > ![Abonnement starten](./media/rp-subscriptions-launch.png)

1. Zodra het abonnement wordt geopend, klikt u aan de linkerkant op **resource providers**. Zoek vervolgens in het rechterdeel venster op *peering* in het zoek venster of gebruik de schuif balk om **micro soft. peering** te zoeken en de **status** te bekijken. Als de status is **_geregistreerd_*, kunt u de onderstaande stappen overs Laan en door gaan naar sectie _* Create PeerAsn**. Als de status **_NotRegistered_*_ is, selecteert u _* micro soft. peering** en klikt u op **registreren**.

    > [!div class="mx-imgBorder"]
    > ![Begin registratie](./media/rp-register-start.png)

1. Houd er rekening mee dat de status wordt gewijzigd in ***registratie***.

    > [!div class="mx-imgBorder"]
    > ![Registratie wordt uitgevoerd](./media/rp-register-progress.png)

1. Wacht totdat de registratie is voltooid. Klik vervolgens op **vernieuwen** en controleer of de status is **_geregistreerd_**.

    > [!div class="mx-imgBorder"]
    > ![Registratie is voltooid](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>PeerAsn maken
Als Internet provider of provider van Internet Exchange kunt u een nieuwe PeerAsn-resource maken voor het koppelen van een autonoom systeem nummer (ASN) aan een Azure-abonnement op de [pagina een peer-ASN koppelen](https://go.microsoft.com/fwlink/?linkid=2129592) . U kunt meerdere Asn's koppelen aan een abonnement door een **PeerAsn** te maken voor elke ASN die u moet koppelen.

1. Vul op de pagina **een peer-ASN koppelen** onder het tabblad **basis beginselen** de velden in zoals hieronder wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Tabblad PeerAsn-basis](./media/peerasn-basics-tab.png)

    * De **naam** komt overeen met de resource naam en kan alles zijn wat u kiest.  
    * Kies het **abonnement** waaraan u het ASN wilt koppelen.
    * De naam van de **peer** komt overeen met de naam van uw bedrijf en moet zo dicht mogelijk bij uw PeeringDB-profiel staan. Houd er rekening mee dat waarde alleen de tekens a-z, A-Z en spatie ondersteunt
    * Voer uw ASN in het veld **peer ASN** in.
    * Klik op **nieuwe maken** en voer een **e-mail adres** en **telefoon nummer** in voor uw netwerk Operations Center (NOC)
1. Klik vervolgens op **controleren + maken** en houd er rekening mee dat de basis validatie van de gegevens die u hebt ingevoerd, wordt uitgevoerd. Dit wordt weer gegeven in een lint bovenaan, zoals het *uitvoeren van definitieve validatie...*.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het tabblad een peer A S N-basis beginselen koppelen.](./media/peerasn-review-tab-validation.png)

1. Wanneer het bericht in het lint wordt *gevalideerd*, controleert u uw gegevens en verzendt u de aanvraag door op **maken** te klikken. Als de validatie niet is geslaagd, klikt u op **vorige** en herhaalt u de bovenstaande stappen om uw aanvraag aan te passen en te controleren of de ingevoerde waarden geen fouten bevatten.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het tabblad een peer A S N-basis beginselen koppelen waarvoor validatie is geslaagd.](./media/peerasn-review-tab.png)

1. Nadat u de aanvraag hebt ingediend, wacht u totdat de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn geslaagd](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>De status van een PeerAsn weer geven
Zodra de PeerAsn-resource is geïmplementeerd, moet u wachten tot micro soft de koppelings aanvraag heeft goedgekeurd. Het kan tot 12 uur duren voordat goed keuring is uitgevoerd. Na goed keuring ontvangt u een melding voor het e-mail adres dat is ingevoerd in de bovenstaande sectie.

> [!IMPORTANT]
> Wacht totdat de ValidationState is ingeschakeld voordat een peering-aanvraag wordt verzonden. Het kan tot 12 uur duren voordat deze goed keuring is uitgevoerd.

## <a name="modify-peerasn"></a>PeerAsn wijzigen
Het wijzigen van PeerAsn wordt momenteel niet ondersteund. Als u wijzigingen wilt aanbrengen, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>PeerAsn verwijderen
Het is momenteel niet mogelijk om een PeerAsn te verwijderen. Als u PeerAsn moet verwijderen, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Volgende stappen

* [Direct-peering maken of bewerken met de portal](howto-direct-portal.md)
* [Een verouderde Direct-peering converteren in een Azure-resource met de portal](howto-legacy-direct-portal.md)
* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource met de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
