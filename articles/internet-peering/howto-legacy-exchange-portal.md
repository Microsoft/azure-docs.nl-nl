---
title: Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de Azure Portal
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: 6249d68486af754f2d2980aaed9d5fd8287dcb5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700158"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar een Azure-resource met behulp van de Azure Portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) en de [Exchange peering-walkthrough](walkthrough-exchange-all.md) voordat u begint met de configuratie.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Een verouderde Exchange-peering converteren naar een Azure-resource

Als Internet Exchange-provider kunt u een uitwisselings aanvraag maken door [een peering te maken]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de selectie vakjes in, zoals hier wordt weer gegeven:

   ![Peering Service registreren](./media/setup-basics-tab.png)

* Selecteer uw Azure-abonnement.

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
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange-peering controleren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
