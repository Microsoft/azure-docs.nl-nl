---
title: Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal
titleSuffix: Azure
description: Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700260"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Een verouderde directe peering converteren naar een Azure-resource met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een bestaande verouderde directe peering converteert naar een Azure-resource met behulp van de Azure Portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Stapsgewijze instructies voor direct peering](walkthrough-direct-all.md) voordat u begint met de configuratie.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Een verouderde directe peering converteren naar een Azure-resource

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Een verouderde directe peering converteren

Als Internet provider kunt u verouderde directe peering-verbindingen converteren met behulp van het [maken van een peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

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

#### <a name="launch-the-resource-and-configure-basic-settings"></a>De resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
