---
title: Een Exchange-peering maken of wijzigen met behulp van de Azure Portal
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met behulp van de Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700477"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Een Exchange-peering maken of wijzigen met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een micro soft Exchange-peering maakt met behulp van de Azure Portal. In dit artikel wordt ook uitgelegd hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) en de [Exchange peering-walkthrough](walkthrough-exchange-all.md) voordat u begint met de configuratie.
* Als u al Exchange-peers met micro soft hebt die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Een Exchange-peering maken


Als Internet Exchange-provider kunt u een uitwisselings aanvraag maken door [een peering te maken]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de selectie vakjes in, zoals hier wordt weer gegeven:

    > [!div class="mx-imgBorder"] 
    > ![Peering Service registreren](./media/setup-basics-tab.png)

*    Selecteer uw Azure-abonnement.

* Voor resource groep kunt u een bestaande resource groep in de vervolg keuzelijst kiezen of een nieuwe groep maken door nieuw maken te selecteren. We maken een nieuwe resource groep voor dit voor beeld.

* De naam komt overeen met de naam van de resource en kan alles zijn wat u kiest.

* De regio wordt automatisch geselecteerd als u een bestaande resource groep hebt gekozen. Als u ervoor hebt gekozen om een nieuwe resource groep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt opslaan.

>[!NOTE]
>De regio waar een resource groep zich bevindt, is onafhankelijk van de locatie waar u peering met micro soft wilt maken. Maar het is een best practice om uw peering resources te organiseren in resource groepen die zich in de dichtstbijzijnde Azure-regio's bevinden. U kunt bijvoorbeeld voor peerings in Ashburn een resource groep maken in VS-Oost of Oost-VS2.

* Selecteer uw ASN in het vak **PeerASN** .

>[!IMPORTANT] 
>U kunt alleen een ASN met ValidationState als goedgekeurd kiezen voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt verzonden, wacht u 12 uur of voor een ASN-koppeling die u wilt goed keuren. Als de ASN die u hebt geselecteerd, in afwachting is van validatie, wordt een fout bericht weer gegeven. Als u de ASN die u nodig hebt, niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Als dit het geval is, controleert u of u PeerAsn al hebt gemaakt met behulp van **[peer ASN koppelen aan Azure-abonnement](https://go.microsoft.com/fwlink/?linkid=2129592)**.

* Selecteer **volgende: Configuratie** om door te gaan.

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Een Exchange-peering controleren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Inrichting van een Exchange-peering ongedaan maken
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
* [Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.
