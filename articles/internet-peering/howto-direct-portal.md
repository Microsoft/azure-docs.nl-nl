---
title: Een directe peering maken of wijzigen met behulp van de Azure Portal
titleSuffix: Azure
description: Een directe peering maken of wijzigen met behulp van de Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537205"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Een directe peering maken of wijzigen met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een micro soft direct-peering maakt voor een Internet serviceprovider of Internet Exchange-provider met behulp van de Azure Portal. In dit artikel wordt ook uitgelegd hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Power shell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Stapsgewijze instructies voor direct peering](walkthrough-direct-all.md) voordat u begint met de configuratie.
* Als u al directe peering-verbindingen met micro soft hebt die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde directe peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Een directe peering maken en inrichten

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Een directe peering maken

Als Internet provider of provider van Internet Exchange kunt u een nieuwe aanvraag voor directe peering maken door [een peering te maken]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de selectie vakjes in, zoals hier wordt weer gegeven:


    ![Scherm afbeelding toont het tabblad basis beginselen van het maken van een peering pagina met de nieuwe koppeling maken.](./media/setup-basics-tab.png)

2. Selecteer uw Azure-abonnement.

3. Voor resource groep kunt u een bestaande resource groep in de vervolg keuzelijst kiezen of een nieuwe groep maken door nieuw maken te selecteren. We maken een nieuwe resource groep voor dit voor beeld.

4. De naam komt overeen met de naam van de resource en kan alles zijn wat u kiest.

5. De regio wordt automatisch geselecteerd als u een bestaande resource groep hebt gekozen. Als u ervoor hebt gekozen om een nieuwe resource groep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt opslaan.

    >[!NOTE]
    > De regio waar een resource groep zich bevindt, is onafhankelijk van de locatie waar u peering met micro soft wilt maken. Maar het is een best practice om uw peering resources te organiseren in resource groepen die zich in de dichtstbijzijnde Azure-regio's bevinden. U kunt bijvoorbeeld voor peerings in Ashburn een resource groep maken in VS-Oost of Oost-VS2.

6. Selecteer uw ASN in het vak **PeerASN** .

    >[!IMPORTANT]
    >U kunt alleen een ASN met ValidationState als goedgekeurd kiezen voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt verzonden, wacht u 12 uur of voor een ASN-koppeling die u wilt goed keuren. Als de ASN die u hebt geselecteerd, in afwachting is van validatie, wordt een fout bericht weer gegeven. Als u de ASN die u nodig hebt, niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Als dit het geval is, controleert u of u PeerAsn al hebt gemaakt met behulp van **[peer ASN koppelen aan Azure-abonnement](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Selecteer **volgende: Configuratie** om door te gaan.



    ![Scherm afbeelding toont het tabblad basis beginselen van het maken van een peering pagina met alle ingevoerde waarden.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Een directe peering wijzigen
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Inrichting van een directe peering ongedaan maken
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.
