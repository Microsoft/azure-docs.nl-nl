---
title: Groeps beleid maken en beheren in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het bewerken van de ingebouwde groeps beleidsobjecten (Gpo's) en het maken van uw eigen aangepaste beleids regels in een Azure Active Directory Domain Services beheerd domein.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619382"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>groepsbeleid beheren in een Azure Active Directory Domain Services beheerd domein

Instellingen voor gebruikers-en computer objecten in Azure Active Directory Domain Services (Azure AD DS) worden vaak beheerd met behulp van groepsbeleid-objecten (Gpo's). Azure AD DS bevat ingebouwde Gpo's voor de containers *AADDC gebruikers* en *AADDC-computers* . U kunt deze ingebouwde Gpo's aanpassen om groepsbeleid te configureren die nodig zijn voor uw omgeving. Leden van de groep *Azure AD DC-Administrators* hebben Groepsbeleid beheerders bevoegdheden in het Azure AD DS-domein, en kunnen ook aangepaste gpo's en organisatie-eenheden (ou's) maken. Zie [Groepsbeleid Overview][group-policy-overview]voor meer informatie over wat Groepsbeleid is en hoe het werkt.

In een hybride omgeving worden groeps beleidsregels die zijn geconfigureerd in een on-premises AD DS omgeving niet gesynchroniseerd met Azure AD DS. Als u configuratie-instellingen wilt definiëren voor gebruikers of computers in azure AD DS, bewerkt u een van de standaard-Gpo's of maakt u een aangepast groeps beleidsobject.

In dit artikel wordt beschreven hoe u de groepsbeleid-beheer hulpprogramma's installeert, vervolgens de ingebouwde Gpo's bewerkt en aangepaste Gpo's maakt.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een Windows Server Management-VM die deel uitmaakt van het door Azure AD DS beheerde domein.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken van een Windows Server-VM en voegt u deze toe aan een beheerd domein][create-join-windows-vm].
* Een gebruikersaccount dat lid is van de *Azure AD DC-beheerdersgroep* in uw Azure AD-tenant.

> [!NOTE]
> U kunt groepsbeleid Beheersjablonen gebruiken door de nieuwe sjablonen te kopiëren naar het beheer werkstation. Kopieer de *ADMX* -bestanden naar `%SYSTEMROOT%\PolicyDefinitions` en kopieer de taalspecifieke *. ADML* -bestanden naar `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , waarbij `Language-CountryRegion` overeenkomt met de taal en regio van de *. ADML* -bestanden.
>
> Kopieer bijvoorbeeld de Engelse Verenigde Staten versie van de *. ADML* -bestanden naar de `\en-us` map.
>
> U kunt uw groepsbeleid beheer sjabloon ook centraal opslaan op de domein controllers die deel uitmaken van het beheerde domein. Zie [het centraal archief maken en beheren voor groepsbeleid Beheersjablonen in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)voor meer informatie.

## <a name="install-group-policy-management-tools"></a>groepsbeleid-beheer hulpprogramma's installeren

Als u groepsbeleid object (Gpo's) wilt maken en configureren, moet u de groepsbeleid-beheer hulpprogramma's installeren. Deze hulpprogram ma's kunnen worden geïnstalleerd als een functie in Windows Server. Zie install [Remote Server Administration Tools (RSAT) (Engelstalig)][install-rsat]voor meer informatie over het installeren van de beheer Programma's op een Windows-client.

1. Meld u aan bij uw beheer-VM. Zie [verbinding maken met een virtuele machine van Windows Server][connect-windows-server-vm]voor stappen voor het maken van verbinding met behulp van de Azure Portal.
1. **Serverbeheer** moet standaard worden geopend wanneer u zich aanmeldt bij de VM. Als dat niet het geval is, selecteert u **Serverbeheer** in het menu **Start** .
1. Selecteer in het deelvenster *Dashboard* van het venster **Serverbeheer** de optie **Functies en onderdelen toevoegen**.
1. Selecteer op de pagina **Voordat u begint** van de wizard *Functies en onderdelen toevoegen* **Volgende**.
1. Voor het *installatietype* moet u de optie van het selectievakje **Installatie die op de functie of het onderdeel is gebaseerd** ingeschakeld laten en **Volgende** selecteren.
1. Kies op de pagina **Selectie van servers** de huidige VM uit de servergroep, zoals *myvm.aaddscontoso.com*, en selecteer vervolgens **Volgende**.
1. Klik op de pagina **Serverfuncties** op **Volgende**.
1. Selecteer op de pagina **functies** de **Groepsbeleid beheer** functie.

    ![Installeer het ' groepsbeleid-beheer ' op de pagina functies](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Selecteer op de pagina **Bevestiging** **Installeren**. Het kan een paar minuten duren voordat de groepsbeleid-beheer hulpprogramma's zijn geïnstalleerd.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Functies en onderdelen toevoegen** af te sluiten.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Het console Groepsbeleidbeheer openen en een object bewerken

Standaard groeps beleidsobjecten (Gpo's) bestaan voor gebruikers en computers in een beheerd domein. Met de functie voor groepsbeleid beheer die in de vorige sectie is geïnstalleerd, bekijken en bewerken we een bestaand groeps beleidsobject. In de volgende sectie maakt u een aangepast groeps beleidsobject.

> [!NOTE]
> Als u groeps beleid in een beheerd domein wilt beheren, moet u zijn aangemeld bij een gebruikers account dat lid is van de groep *Aad DC-Administrators* .

1. Selecteer in het Start scherm de optie **systeem beheer**. Er wordt een lijst met beschik bare beheer hulpprogramma's weer gegeven, met inbegrip van **Groepsbeleid-beheer** dat in de vorige sectie is geïnstalleerd.
1. Als u de console Groepsbeleidbeheer (GPMC) wilt openen, kiest u **Groepsbeleid beheer**.

    ![De console Groepsbeleidbeheer wordt gereed voor het bewerken van groeps beleidsobjecten](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Er zijn twee ingebouwde groepsbeleid objecten (Gpo's) in een beheerd domein: een voor de container *AADDC computers* en één voor de container gebruikers van *AADDC* . U kunt deze groeps beleidsobjecten aanpassen om groeps beleid te configureren als nodig in uw beheerde domein.

1. Vouw in de **Groepsbeleid-beheer** console het knoop punt **forest: aaddscontoso.com** uit. Vouw vervolgens de knoop punten **domeinen** uit.

    Er bestaan twee ingebouwde containers voor *AADDC-computers* en *AADDC-gebruikers*. Voor elk van deze containers is een standaard groeps beleidsobject toegepast.

    ![Ingebouwde Gpo's die zijn toegepast op de standaard containers AADDC computers en AADDC gebruikers](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Deze ingebouwde groeps beleidsobjecten kunnen worden aangepast voor het configureren van specifieke groeps beleidsregels voor uw beheerde domein. Klik met de rechter muisknop op een van de groeps beleidsobjecten, zoals *AADDC computers*, en kies vervolgens **bewerken...**.

    ![Kies de optie voor het bewerken van een van de ingebouwde Gpo's](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Het hulp programma Groepsbeleidsbeheer-editor wordt geopend, waarmee u het groeps beleidsobject, zoals *account beleid*, kunt aanpassen:

    ![Scherm afbeelding van de Groepsbeleidsbeheer-editor.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Wanneer u klaar bent, kiest u **bestand > opslaan** om het beleid op te slaan. Computers vernieuwen groepsbeleid standaard elke 90 minuten en voeren de wijzigingen toe die u hebt aangebracht.

## <a name="create-a-custom-group-policy-object"></a>Een aangepast groepsbeleid-object maken

Als u gelijksoortige beleids instellingen wilt groeperen, maakt u vaak extra groeps beleidsobjecten in plaats van alle vereiste instellingen toe te passen in de enkelvoudige standaard groeps beleidsobject. Met Azure AD DS kunt u uw eigen aangepaste groeps beleidsobjecten maken of importeren en deze koppelen aan een aangepaste organisatie-eenheid. Als u eerst een aangepaste OE wilt maken, raadpleegt u [een aangepaste organisatie-eenheid maken in een beheerd domein](create-ou.md).

1. Selecteer in de **Groepsbeleid-beheer** console uw aangepaste organisatie-eenheid (OE), bijvoorbeeld *MyCustomOU*. Klik met de rechter muisknop op de OE en kies **een groeps beleidsobject in dit domein maken en hier een koppeling aanbrengen...**:

    ![Een aangepast groeps beleidsobject maken in de groepsbeleid-beheer console](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Geef een naam op voor het nieuwe groeps beleidsobject, zoals *Mijn aangepaste GPO*, en selecteer vervolgens **OK**. U kunt eventueel dit aangepaste GPO baseren op een bestaande groeps beleidsobject en een set met beleids opties.

    ![Geef een naam op voor het nieuwe aangepaste GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Het aangepaste groeps beleidsobject wordt gemaakt en gekoppeld aan uw aangepaste organisatie-eenheid. Als u de beleids instellingen nu wilt configureren, selecteert u het aangepaste groeps beleidsobject en kiest u **bewerken...**:

    ![Kies de optie voor het bewerken van uw aangepaste GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. De **Groepsbeleidsbeheer-editor** wordt geopend, waarmee u het groeps beleidsobject kunt aanpassen:

    ![Groeps beleidsobject aanpassen om instellingen te configureren zoals vereist](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Wanneer u klaar bent, kiest u **bestand > opslaan** om het beleid op te slaan. Computers vernieuwen groepsbeleid standaard elke 90 minuten en voeren de wijzigingen toe die u hebt aangebracht.

## <a name="next-steps"></a>Volgende stappen

Zie [werken met Groepsbeleid voorkeurs items][group-policy-console]voor meer informatie over de beschik bare Groepsbeleid-instellingen die u kunt configureren met behulp van de console Groepsbeleidbeheer.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
