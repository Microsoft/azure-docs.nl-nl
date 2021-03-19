---
title: Firewall regels beheren-Azure Portal-Azure Database for MySQL-flexibele server
description: Maak en beheer firewall regels voor Azure Database for MySQL-flexibele server met behulp van de Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 132319575147c2ff1075881b1f1faec8bc5029f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936632"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Maak en beheer firewall regels voor Azure Database for MySQL-flexibele server met behulp van de Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL Flexibele server ondersteunt twee typen elkaar wederzijds uitsluitende netwerkverbindingsmethoden om verbinding te maken met uw flexibele server. De twee opties zijn:

1. Openbare toegang (toegestane IP-adressen)
2. Privétoegang (VNet-integratie)

In dit artikel wordt de nadruk gelegd op het maken van MySQL-server met **open bare toegang (toegestane IP-adressen)** met behulp van Azure Portal en biedt een overzicht van het beheren van firewall regels na het maken van een flexibele server. Met *open bare toegang (toegestane IP-adressen)* worden de verbindingen met de mysql-server beperkt tot toegestane IP-adressen. De IP-adressen van de client moeten worden toegestaan in firewall regels. Raadpleeg [open bare toegang (toegestane IP-adressen)](./concepts-networking.md#public-access-allowed-ip-addresses)voor meer informatie hierover. De firewall regels kunnen worden gedefinieerd op het moment dat de server wordt gemaakt (aanbevolen), maar kunnen ook later worden toegevoegd. In dit artikel vindt u een overzicht van het maken en beheren van firewall regels met behulp van open bare toegang (toegestane IP-adressen).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Een firewall regel maken bij het maken van een server

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).
2. Selecteer **Databases** > **Azure Database voor MySQL**. U kunt ook **MySQL** in het zoekvak typen om de service te vinden.
3. Selecteer **Flexibele server** als implementatieoptie.
4. Vul het formulier **basis principes** in.
5. Ga naar het tabblad **netwerken** om te configureren hoe u verbinding wilt maken met uw server.
6. Selecteer in de **verbindings methode** *open bare toegang (toegestane IP-adressen)*. Als u de **firewall regels** wilt maken, geeft u de naam van de firewall regel en één IP-adres of een bereik van adressen op. Als u de regel tot één IP-adres wilt beperken, typt u hetzelfde adres in het veld voor eerste IP-adres en laatste IP-adres. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot alle data bases op de MySQL-server waarvoor ze geldige referenties hebben.
   > [!Note]
   > Azure Database for MySQL flexibele server maakt een firewall op server niveau. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij u een firewallregel maakt om de firewall te openen voor specifieke IP-adressen.

7. Selecteer **Beoordelen + maken** om uw flexibele serverconfiguratie te controleren.
8.  Selecteer **Maken** om de server in te richten. Het inrichten kan enkele minuten duren.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Een firewall regel maken nadat de server is gemaakt

1. Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure database for MySQL flexibele server waarop u firewall regels wilt toevoegen.
2. Klik op de pagina flexibele server, onder **instellingen** kop, op **netwerken** om de pagina netwerk te openen voor flexibele server.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. Klik op **huidige client-IP-adres toevoegen** in de firewall regels. Hiermee wordt automatisch een firewall regel gemaakt met het open bare IP-adres van uw computer, zoals wordt waargenomen door het Azure-systeem.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige gevallen wijkt het IP-adres van Azure Portal af van het IP-adres dat wordt gebruikt voor toegang tot het internet en Azure-servers. Daarom moet u mogelijk het IP-begin adres en het laatste IP-adres wijzigen om de regel te laten werken zoals verwacht.

   U kunt een zoek machine of een ander online programma gebruiken om uw eigen IP-adres te controleren. Zoek bijvoorbeeld naar ' wat is mijn IP '.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. Voeg extra adresbereiken toe. In de firewall regels voor de Azure Database for MySQL flexibele server kunt u één IP-adres of een bereik van adressen opgeven. Als u de regel tot één IP-adres wilt beperken, typt u hetzelfde adres in het veld voor eerste IP-adres en laatste IP-adres. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot alle data bases op de MySQL-server waarvoor ze geldige referenties hebben.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. Klik op **Opslaan** op de werk balk om deze firewall regel op te slaan. Wacht tot de bevestiging is geslaagd voor de update van de firewall regels.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>Verbinding maken vanuit Azure

Mogelijk wilt u resources of toepassingen die zijn geïmplementeerd in azure inschakelen om verbinding te maken met uw flexibele server. Dit omvat webtoepassingen die worden gehost in Azure App Service, die worden uitgevoerd op een virtuele machine van Azure, een Azure Data Factory Data Management Gateway en nog veel meer.

Wanneer een toepassing in azure probeert verbinding te maken met uw server, controleert de firewall of Azure-verbindingen zijn toegestaan. U kunt deze instelling inschakelen door de optie **open bare toegang vanaf Azure-Services en-resources in azure toestaan** in de portal op het tabblad **netwerken** te selecteren en op **Opslaan** te drukken.

De resources hoeven zich niet in hetzelfde virtuele netwerk (VNet) of resource groep voor de firewall regel te bevinden om deze verbindingen in te scha kelen. Als de verbindings poging niet is toegestaan, bereikt de aanvraag niet de Azure Database for MySQL flexibele server.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
>
> U kunt het beste de **persoonlijke toegang (VNet-integratie)** kiezen voor een veilige toegang tot flexibele server.
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Bestaande firewall regels beheren via de Azure Portal

Herhaal de volgende stappen om de firewall regels te beheren.

- Als u de huidige computer wilt toevoegen, klikt u op + **huidige client-IP-adres toevoegen** in de firewall regels. Klik op **Opslaan** om de wijzigingen op te slaan.
- As u extra IP-adressen wilt toevoegen, typt u de Regelnaam, het Eerste IP-adres en het Laatste IP-adres in. Klik op **Opslaan** om de wijzigingen op te slaan.
- Als u een bestaande regel wilt wijzigen, klikt u op een willekeurig veld in de regel om dit aan te passen. Klik op **Opslaan** om de wijzigingen op te slaan.
- Als u een bestaande regel wilt verwijderen, klikt u op het beletsel teken [...] en klikt u op **verwijderen** om de regel te verwijderen. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in azure database for MySQL flexibele server](./concepts-networking.md)
- Meer informatie over [Azure database for MySQL flexibele server firewall regels](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Azure database for MySQL firewall regels maken en beheren met behulp van Azure cli](./how-to-manage-firewall-cli.md).