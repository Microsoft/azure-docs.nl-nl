---
title: Peering-verbinding voor Exchange-partners met route server met behulp van de portal
titleSuffix: Azure
description: Een Exchange-peering met route server maken of wijzigen met behulp van de Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537188"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Een Exchange-peering maken of wijzigen met route server in Azure Portal

In dit artikel wordt beschreven hoe u een micro soft Exchange-peering met een route server maakt met behulp van de Azure Portal. In dit artikel wordt ook uitgelegd hoe u de status van de resource kunt controleren, bijwerken of verwijderen.


## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) en de [Exchange peering-walkthrough](walkthrough-exchange-all.md) voordat u begint met de configuratie.
* Als u al Exchange-peers met micro soft hebt die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Een Exchange-peering maken met route server


Als Internet Exchange-provider kunt u een uitwisselings aanvraag maken door [een peering te maken]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de selectie vakjes in, zoals hier wordt weer gegeven:

    > [!div class="mx-imgBorder"] 
    > ![Peering Service registreren](./media/setup-basics-tab.png)

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

1. Vul op de pagina een peering maken op het tabblad Configuratie de selectie vakjes in, zoals hier wordt weer gegeven:

    > [!div class="mx-imgBorder"]
    > ![Route server configureren](./media/setup-exchange-conf-tab-routeserver.png)
 
    * Voor peering-type selecteert u **direct**
    * Selecteer voor micro soft-netwerk **AS8075 met Exchange route server**. 
    * Selecteer SKU als **gratis basis** versie. Selecteer Premium gratis omdat deze is gereserveerd voor speciale toepassingen.
    * Selecteer de **metro** locatie waar u peering wilt instellen.

1. Onder **peering verbindingen** selecteert u **nieuwe maken**

1.  Vul onder **directe peering verbinding** de volgende BGP-sessie gegevens in:

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het deel venster directe peering-verbinding met details toegevoegd.](./media/setup-exchange-conf-tab-direct-route.png)


     * Peering-faciliteit, selecteer de juiste fysieke locatie voor de peering
     * Sessie adres provider, peer selecteren
     * IPv4-voor voegsel voor de sessie wordt verschaft door de peer van de Exchange-provider
     * Het IPv4-adres van de peer-sessie wordt geselecteerd door de Exchange-peer voor de route server uit het bereik van de IP-voor voegsel.
     * Het IPv4-adres van micro soft-sessies is de router-IP die is toegewezen vanuit het bereik IP-voor voegsel.
     * De sessie-IPv6 is op dit moment optioneel.
     * Het maximum aantal aangekondigde IPv4-voor voegsels kan Maxi maal 20000. 
     * Gebruiken voor peering-service is standaard uitgeschakeld. De functie kan worden ingeschakeld zodra de Exchange-provider een peering-service overeenkomst met micro soft heeft ondertekend.

1. Klik na voltooiing op **Opslaan**. 

1. Onder een peering maken ziet u de validatie is voltooid. Nadat de validatie is voltooid, klikt u op **maken**

    > [!div class="mx-imgBorder"]
    > ![Validatie van instellingen](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >Voor normale Internet serviceproviders (ISP) die een micro soft-peering service-partner zijn, is registratie van klant-IP-voor voegsels vereist. In het geval van Exchange-partners met een route server is het echter nodig om klant Asn's en niet-voor voegsels te registreren. Dezelfde ASN-sleutel is geldig voor de registratie van de voor voegsel van de klant.

1. Selecteer **geregistreerde asn's** in het gedeelte instellingen.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het deel venster voor peering met de geregistreerde A S NS menu-item met de naam.](./media/setup-exchange-registered-asn.png)

1. Selecteer **geregistreerde ASN toevoegen** om een nieuwe klant-ASN te maken onder uw Exchange-abonnement.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het deel venster A S N registreren met een naam en een S N-tekst vakken.](./media/setup-exchange-register-new-asn.png)

1. Onder een ASN registreren selecteert u een naam, vult u de klant-ASN in en klikt u op opslaan.

1. Onder geregistreerde Asn's wordt een bijbehorende voorvoegsel sleutel toegewezen aan elke ASN. Als Exchange-provider moet u deze voorvoegsel sleutel opgeven aan uw klant zodat deze peering service kan registreren onder hun abonnement.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont het deel venster geregistreerde A S NS met voorvoegsel sleutels.](./media/setup-exchange-register-asn-prefixkey.png)




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
