---
title: Windows Virtual Desktop, hostgroep, Azure portal - Azure
description: Een Windows Virtual Desktop-hostgroep maken met behulp van de Azure-portal.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 60566b95447c1b69fb257435f45a11524ac5d8b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617337"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Zelfstudie: Een hostpool maken met de Azure-portal

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt. Objecten die u met Windows Virtual Desktop (klassiek) maakt, kunnen niet worden beheerd met Azure Portal.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines (VM's) in Windows Virtual Desktop-omgevingen. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureaublad.

Dit artikel begeleidt u stapsgewijs door het installatieproces voor het maken van een hostgroep voor een Windows Virtual Desktop-omgeving van Windows via de Azure-portal. Deze methode biedt een gebruikersinterface op basis van een browser om een hostgroep in Windows Virtual Desktop te maken, een resourcegroep met VM's in een Azure-abonnement te maken, deze VM's toe te voegen aan het Azure Active Directory-domein (AD) en de VM's te registreren bij Windows Virtual Desktop.

## <a name="prerequisites"></a>Vereisten

U moet de volgende parameters invoeren om een hostgroep te maken:

- De naam van de VM-installatiekopie
- VM-configuratie
- Eigenschappen van domein en netwerk
- Eigenschappen van Windows Virtual Desktop-hostgroep

Ook moet u het volgende weten:

- Waar de bron van de installatiekopie die u wilt gebruiken zich bevindt. Komt deze uit de Azure-galerie of is het een aangepaste installatiekopie?
- Uw referenties voor domeindeelname.

Zorg er ook voor dat u de resourceprovider Microsoft.DesktopVirtualization hebt geregistreerd. Ga, als u dit nog niet hebt gedaan, naar **Abonnementen**, selecteer de naam van uw abonnement en vervolgens **Resourceproviders**. Zoek naar DesktopVirtualization, selecteer Microsoft. DesktopVirtualization en selecteer vervolgens Registreren.

Wanneer u een Windows Virtual Desktop-hostgroep maakt met de Azure Resource Manager-sjabloon, kunt u een virtuele machine maken vanuit de Azure-galerie, namelijk een beheerde installatiekopie of een niet-beheerde installatiekopie. Zie [Een Windows-VHD of -VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en [Een beheerde installatiekopie van een gegeneraliseerde VM maken in Azure](../virtual-machines/windows/capture-image-resource.md) voor meer informatie over het maken van VM-installatiekopieën.

Als u nog geen Azure-abonnement hebt, moet u [een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u aan de slag gaat met deze instructies.

## <a name="begin-the-host-pool-setup-process"></a>Het installatieproces van de hostgroep starten

Ga als volgt te werk om te beginnen met het maken van uw nieuwe hostgroep:

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Als u zich aanmeldt bij de Amerikaanse overheidsportal, gaat u in plaats daarvan naar [https://portal.azure.us/](https://portal.azure.us/).

2. Voer **Windows Virtual Desktop** op de zoekbalk in en selecteer **Windows Virtual Desktop** onder Services.

3. Selecteer op de overzichtspagina van **Windows Virtual Desktop** de optie **Een hostgroep maken**.

4. Selecteer op het tabblad **Basisinformatie** het juiste abonnement onder Projectgegevens.

5. Selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken of selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

6. Voer een unieke naam voor de hostgroep in.

7. Selecteer in het veld Locatie de regio waar u de hostgroep wilt maken in de vervolgkeuzelijst.

   De Azure-geografie die is gekoppeld aan de regio's die u hebt geselecteerd, is de locatie waar de metagegevens voor deze hostgroep en de gerelateerde objecten worden opgeslagen. Zorg ervoor dat u de regio's in de geografie kiest waarin u wilt dat de metagegevens van de service worden opgeslagen.

     > [!div class="mx-imgBorder"]
     > ![Een schermopname van de Azure Portal met het veld Locatie met de locatie US - oost geselecteerd. Naast het veld ziet u de tekst: "Metagegevens worden opgeslagen in US - oost."](media/portal-location-field.png)
  
   >[!NOTE]
   > Als u uw hostgroep in [een ondersteunde regio](data-locations.md) buiten de Verenigde Staten wilt maken, moet u de resource provider opnieuw registreren. Na het opnieuw registreren ziet u de andere regio's in de vervolg keuzelijst voor het selecteren van de locatie. Meer informatie over het opnieuw registreren van het probleem bij het maken van een [hostgroep](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) .

8. Onder Hostgroeptype geeft u aan of uw hostgroep **Persoonlijk** of **Gegroepeerd** wordt.

    - Als u **Persoonlijk** kiest, selecteert u vervolgens **Automatisch** of **Direct** in het veld Toewijzingstype.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van het vervolgkeuzemenu bij het veld Toewijzingstype. De gebruiker heeft Automatisch geselecteerd.](media/assignment-type-field.png)

9.  Als u **Gegroepeerd** kiest, voert u de volgende gegevens in:

     - Voor **Maximale sessielimiet** voert u het maximum aantal gebruikers in voor wie u gelijke taakverdeling voor één sessiehost wilt.
     - Kies voor **Algoritme voor taakverdeling** de optie breedte-eerst of diepte-eerst, op basis van uw gebruikspatroon.

       > [!div class="mx-imgBorder"]
       > ![Een schermopname van het veld Toewijzingstype met "Gegroepeerd" geselecteerd. De gebruiker beweegt de cursor over Breedte-eerst in het vervolgkeuzemenu voor gelijke taakverdeling.](media/pooled-assignment-type.png)

10. Selecteer **Volgende: Virtual Machines >** .

11. Als u al virtuele machines hebt gemaakt en deze wilt gebruiken voor de nieuwe hostgroep, selecteert u **Nee** en vervolgens **Volgende: Werkruimte >** en ga naar de sectie [Werkruimtegegevens](#workspace-information). Als u nieuwe virtuele machines wilt maken en deze wilt registreren bij de nieuwe hostgroep, selecteert u **Ja**.

Nu u het eerste deel hebt voltooid, gaan we verder met het volgende deel van het installatieproces waarin de virtuele machine wordt gemaakt.

## <a name="virtual-machine-details"></a>Details van virtuele machine

Nu we het eerste deel hebben afgerond, moet u uw virtuele machine gaan instellen.

Ga als volgt te werk om uw virtuele machine in te stellen binnen het installatieproces van de hostgroep:

1. Kies onder **Resourcegroep** de resourcegroep waarin u de virtuele machines wilt maken. Dit kan een andere resourcegroep zijn dan die u hebt gebruikt voor de hostgroep.

2. Daarna geeft u een **Naamvoorvoegsel** op om de virtuele machines die door het installatieproces zijn gemaakt een naam te geven. Het achtervoegsel wordt `-` met getallen die beginnen bij 0.

3. Kies de **locatie van de virtuele machine** waarin u de virtuele machines wilt maken. Deze kunnen hetzelfde zijn of verschillen van de regio die u hebt geselecteerd voor de hostgroep.
   
4. Kies vervolgens de optie voor de beschik baarheid die het beste bij uw behoeften past. Zie [beschikbaarheids opties voor virtuele machines in azure](../virtual-machines/availability.md) en [onze veelgestelde vragen](faq.md#which-availability-option-is-best-for-me)voor meer informatie over welke optie geschikt is voor u.
   
   > [!div class="mx-imgBorder"]
   > [Een scherm opname van de vervolg keuzelijst beschik bare zone. De optie ' beschikbaarheids zone ' is gemarkeerd.](media/availability-zone.png)

5. Kies vervolgens de installatiekopie die moet worden gebruikt om de virtuele machine te maken. U kunt kiezen tussen **Galerie** en **Opslagblob**.

    - Als u **Galerie** kiest, selecteert u een van de aanbevolen installatiekopieën in het vervolgkeuzemenu:

      - Windows 10 Enterprise voor meerdere sessies, versie 1909
      - Windows 10 Enterprise voor meerdere sessies, versie 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise voor meerdere sessies, versie 2004
      - Windows 10 Enterprise voor meerdere sessies, versie 2004 + Microsoft 365 Apps

      Als u de gewenste installatie kopie niet ziet, selecteert u **alle afbeeldingen weer geven**, waarmee u een andere afbeelding in uw galerie of een afbeelding van micro soft en andere uitgevers kunt selecteren. Zorg ervoor dat de afbeelding die u kiest een van de [ondersteunde installatie kopieën van het besturings systeem](overview.md#supported-virtual-machine-os-images)is.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van de Marketplace met een lijst met installatiekopieën van Microsoft.](media/marketplace-images.png)

      U kunt ook naar **Mijn objecten** gaan en een aangepaste installatiekopie kiezen die u al hebt geüpload.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van het tabblad Mijn objecten.](media/my-items.png)

    - Als u **opslag-BLOB** kiest, kunt u uw eigen installatie kopie maken met behulp van Hyper-V of op een Azure VM. U hoeft alleen de locatie van de installatiekopie in de opslagblob als een URI in te voeren.
   
   De locatie van de installatie kopie is onafhankelijk van de optie voor de beschik baarheid, maar de zone tolerantie van de afbeelding bepaalt of die afbeelding kan worden gebruikt in combi natie met een beschikbaarheids zone. Als u een beschikbaarheids zone selecteert tijdens het maken van uw installatie kopie, moet u ervoor zorgen dat u een installatie kopie uit de galerie gebruikt waarvoor zone tolerantie is ingeschakeld. Raadpleeg [de veelgestelde vragen](faq.md#which-availability-option-is-best-for-me)voor meer informatie over de optie voor zone tolerantie die u moet gebruiken.

6. Vervolgens kiest u de grootte van de **virtuele machine** die u wilt gebruiken. U kunt de standaardgrootte behouden of **Formaat wijzigen** selecteren om de grootte te wijzigen. Als u **Formaat wijzigen** selecteert, kiest u in het venster dat verschijnt de grootte van de virtuele machine die geschikt is voor de workload.

7. Geef bij **Aantal virtuele machines** het aantal virtuele machines op dat u voor de hostgroep wilt maken.

    >[!NOTE]
    >Het installatieproces kan maximaal 400 VM's maken tijdens het instellen van uw hostgroep, en tijdens elk VM-installatieproces worden vier objecten in de resourcegroep gemaakt. Tijdens het maken wordt het quotum voor uw abonnement niet gecontroleerd. Controleer daarom of het aantal VM's dat u invoert binnen de limieten voor virtuele machines en API's van Azure voor uw resourcegroep en abonnement valt. U kunt meer VM's toevoegen als u klaar bent met het maken van de hostgroep.

8. Kies welk type besturingssysteemschijven u wilt gebruiken voor uw VM's: Standard - SSD, Premium - SSD of Standard-HDD.

9. Onder Netwerk en beveiliging selecteert u het **virtuele netwerk** en het **subnet** waar u de virtuele machines die u maakt, wilt plaatsen. Zorg ervoor dat het virtuele netwerk verbinding kan maken met de domeincontroller omdat u de virtuele machines in het virtuele netwerk moet toevoegen aan het domein. De DNS-servers van het virtuele netwerk dat u hebt geselecteerd, moeten worden geconfigureerd om het IP-adres van de domeincontroller te gebruiken.

10. Selecteer het gewenste type beveiligingsgroep: **Basis**, **Geavanceerd** of **Geen**.

    Als u **Basis** selecteert, moet u selecteren of u een binnenkomende poort wilt openen. Als u **Ja** selecteert, maakt u een keuze uit de lijst met standaardpoorten om binnenkomende verbindingen naar toe te staan.

    >[!NOTE]
    >Voor een betere beveiliging wordt u aangeraden geen openbare binnenkomende poorten te openen.

    > [!div class="mx-imgBorder"]
    > ![Een schermopname van de paginabeveiligingsgroep waarop een lijst met beschikbare poorten in een vervolgkeuzemenu wordt weergegeven.](media/available-ports.png)

    Als u **Geavanceerd** kiest, selecteert u een bestaande netwerkbeveiligingsgroep die u al hebt geconfigureerd.

11. Selecteer daarna of u wilt dat de virtuele machines aan een specifiek domein en een specifieke organisatie-eenheid worden gekoppeld. Als u **Ja** kiest, geeft u het domein op dat u wilt toevoegen. U kunt eventueel een specifieke organisatie-eenheid toevoegen waarin u de virtuele machines wilt plaatsen. Als u **Nee** kiest, worden de virtuele machines gekoppeld aan het domein dat overeenkomt met het achtervoegsel van de **UPN van de AD-domeindeelname**.

    - Wanneer u een OE opgeeft, moet u ervoor zorgen dat u het volledige pad (Distinguished Name) en zonder aanhalings tekens gebruikt.

12. Voer onder domein beheerders account de referenties in voor de Active Directory-domein beheerder van het virtuele netwerk dat u hebt geselecteerd. Voor dit account kan geen Multi-Factor Authentication (MFA) zijn ingeschakeld. Wanneer u lid wordt van een Azure Active Directory Domain Services-domein (Azure AD DS), moet het account deel uitmaken van de Azure AD DC-beheerdersgroep en moet het accountwachtwoord werken in Azure AD DS.

13. Selecteer **Volgende: Werkruimte >** .

Met deze optie kunt u de volgende fase voor het instellen van uw hostgroep starten: uw app-groep registreren bij een werkruimte.

## <a name="workspace-information"></a>Werkruimtegegevens

Met het installatieproces van de hostgroep wordt standaard een bureaubladtoepassingsgroep gemaakt. Als u wilt dat de hostgroep werkt zoals bedoeld, moet u deze app-groep publiceren naar gebruikers of gebruikersgroepen en moet u de app-groep registreren bij een werkruimte.

Ga als volgt te werk om de bureaubladtoepassingsgroep te registreren bij een werkruimte:

1. Selecteer **Ja**.

   Als u **Nee** selecteert, kunt u de app-groep later registreren. We raden u echter aan de registratie van de werkruimte in te stellen zodra u dit kunt doen, zodat uw hostgroep goed werkt.

2. Kies vervolgens of u een nieuwe werkruimte wilt maken of een bestaande werkruimte wilt selecteren. De app-groep kan alleen worden geregistreerd bij werkruimten die op dezelfde locatie als de hostgroep zijn gemaakt.

3. Selecteer desgewenst **Volgende: Tags >** .

    Hier kunt u labels toevoegen, zodat u de objecten kunt groeperen met metagegevens waarmee u het voor uw beheerders gemakkelijker maakt.

4. Selecteer als u klaar bent de optie **Beoordelen en maken**.

     >[!NOTE]
     >Het validatieproces Controleren en maken controleert niet of uw wachtwoord voldoet aan de beveiligingsnormen en of uw architectuur juist is. U moet daarom zelf controleren of er problemen zijn met een van deze zaken.

5. Bekijk de informatie over uw implementatie om te controleren of alles juist is. Als u gereed bent, selecteert u **Maken**. Hiermee wordt het implementatieproces gestart, waarmee de volgende objecten worden gemaakt:

     - Uw nieuwe hostgroep.
     - Een bureaublad-app-groep.
     - Een werkruimte, als u ervoor hebt gekozen om deze te maken.
     - Als u ervoor hebt gekozen om de bureaublad-app-groep te registreren, is de registratie voltooid.
     - Virtuele machines, als u deze hebt gemaakt, die zijn gekoppeld aan het domein en die zijn geregistreerd bij de nieuwe hostgroep.
     - Een downloadkoppeling voor een Azure Resource Management-sjabloon op basis van uw configuratie.

Daarna bent u klaar.

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>De Azure Resource Manager-sjabloon uitvoeren om een nieuwe hostgroep in te richten

Als u liever gebruikmaakt van een geautomatiseerd proces, kunt u [onze Azure Resource Manager-sjabloon downloaden](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) om uw nieuwe hostgroep in te richten.

>[!NOTE]
>Als u een geautomatiseerd proces gebruikt om uw omgeving te bouwen, hebt u de nieuwste versie van het JSON-configuratiebestand nodig. Het JSON-bestand is [hier](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list) te vinden.

## <a name="next-steps"></a>Volgende stappen

Nu u uw hostgroep hebt gemaakt, kunt u deze vullen met RemoteApp-programma's. Ga voor meer informatie over het beheren van apps in Windows Virtual Desktop naar onze volgende zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: App-groepen beheren](./manage-app-groups.md)
