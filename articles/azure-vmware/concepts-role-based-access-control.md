---
title: Concepten-vSphere op rollen gebaseerd toegangs beheer (vSphere RBAC)
description: Meer informatie over de belangrijkste mogelijkheden van toegangs beheer op basis van rollen voor Azure VMware-oplossing
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c2d27531f7a0acd36b4047e98aac994668f64a09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586162"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>vSphere op rollen gebaseerd toegangs beheer (vSphere RBAC) voor Azure VMware-oplossing

In de Azure VMware-oplossing heeft vCenter een ingebouwde lokale gebruiker met de naam cloudadmin en toegewezen aan de ingebouwde CloudAdmin-rol. De lokale cloudadmin-gebruiker wordt gebruikt om gebruikers in AD in te stellen. In het algemeen maakt en beheert de rol CloudAdmin werk belastingen in uw privécloud. In azure VMware-oplossing heeft de rol CloudAdmin vCenter-bevoegdheden die verschillen van andere VMware-cloud oplossingen.     

> [!NOTE]
> Met de Azure VMware-oplossing kunt u aangepaste rollen voor vCenter bieden die niet beschikbaar zijn op de Azure VMware-oplossings Portal. Zie de sectie [aangepaste rollen maken in vCenter](#create-custom-roles-on-vcenter) verderop in dit artikel voor meer informatie. 

In een installatie van vCenter en ESXi on-premises heeft de beheerder toegang tot het vCenter- administrator@vsphere.local account. Er kunnen ook meer Active Directory (AD) gebruikers/groepen worden toegewezen. 

In een implementatie van een Azure VMware-oplossing heeft de beheerder geen toegang tot het gebruikers account van de beheerder. Maar ze kunnen AD-gebruikers en-groepen toewijzen aan de CloudAdmin-rol in vCenter.  

De gebruiker van de privécloud heeft geen toegang en kan geen specifieke beheer onderdelen configureren die door micro soft worden ondersteund en beheerd. Bijvoorbeeld clusters, hosts, gegevens opslag en gedistribueerde virtuele switches.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Azure VMware-oplossing CloudAdmin Role in vCenter

U kunt de bevoegdheden weer geven die zijn verleend aan de Azure VMware Solution CloudAdmin-rol in uw Azure VMware-oplossing privécloud-vCenter.

1. Meld u aan bij vCenter en ga naar **menu**  >  **beheer**.
1. Onder **Access Control** selecteert u **rollen**.
1. Selecteer **CloudAdmin** in de lijst met rollen en selecteer vervolgens **bevoegdheden**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="De bevoegdheden voor CloudAdmin in vSphere-client weer geven":::

De CloudAdmin-rol in de Azure VMware-oplossing heeft de volgende bevoegdheden op vCenter. Raadpleeg de [VMware-product documentatie](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) voor een gedetailleerde uitleg van elke bevoegdheid.

| Bevoegdheid | Description |
| --------- | ----------- |
| **Waarschuwingen** | Waarschuwing bevestigen<br />Alarm maken<br />Waarschuwings actie uitschakelen<br />Waarschuwing wijzigen<br />Waarschuwing verwijderen<br />Waarschuwings status instellen |
| **Machtigingen** | Machtigingen wijzigen<br />Rol wijzigen |
| **Inhoudsbibliotheek** | Bibliotheek item toevoegen<br />Een abonnement voor een gepubliceerde bibliotheek maken<br />Lokale bibliotheek maken<br />Geabonneerde bibliotheek maken<br />Bibliotheek item verwijderen<br />Lokale bibliotheek verwijderen<br />Geabonneerde bibliotheek verwijderen<br />Het abonnement van een gepubliceerde bibliotheek verwijderen<br />Bestanden downloaden<br />Bibliotheek items verwijderen<br />Geabonneerde bibliotheek verwijderen<br />Opslag importeren<br />Abonnements gegevens testen<br />Een bibliotheek item publiceren naar de abonnee servers<br />Een bibliotheek publiceren naar de abonnee servers<br />Opslag lezen<br />Bibliotheek item synchroniseren<br />Geabonneerde bibliotheek synchroniseren<br />Type introspectie<br />Configuratie-instellingen bijwerken<br />Update bestanden<br />Bibliotheek bijwerken<br />Bibliotheek item bijwerken<br />Lokale bibliotheek bijwerken<br />Geabonneerde bibliotheek bijwerken<br />Abonnement van een gepubliceerde bibliotheek bijwerken<br />Configuratie-instellingen weer geven |
| **Cryptografische bewerkingen** | Directe toegang |
| **Gegevensarchief** | Ruimte toewijzen<br />Door gegevensarchief bladeren<br />Gegevens opslag configureren<br />Bestands bewerkingen op laag niveau<br />Bestanden verwijderen<br />Meta gegevens van de virtuele machine bijwerken |
| **Map** | Map maken<br />Map verwijderen<br />Map verplaatsen<br />Mapnaam wijzigen |
| **Globaal** | Taak annuleren<br />Global-Tag<br />Gezondheidszorg<br />Logboek gebeurtenis<br />Aangepaste kenmerken beheren<br />Service managers<br />Aangepast kenmerk instellen<br />Systeem label |
| **Host** | vSphere-replicatie<br />Replicatie &#160;&#160;&#160;&#160;beheren |
| **Tags voor vSphere** | VSphere-tag toewijzen en de toewijzing opheffen<br />VSphere-tag maken<br />VSphere-label categorie maken<br />VSphere-tag verwijderen<br />VSphere-label categorie verwijderen<br />VSphere-tag bewerken<br />Categorie vSphere-code bewerken<br />Het veld UsedBy voor categorie wijzigen<br />UsedBy veld voor label wijzigen |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Aanbeveling Toep assen<br />VApp toewijzen aan resource groep<br />Virtuele machine aan resource groep toewijzen<br />Resource groep maken<br />Virtuele machine die is uitgeschakeld migreren<br />De ingeschakelde virtuele machine wordt gemigreerd<br />Resource groep wijzigen<br />Resource groep verplaatsen<br />Query's van vMotion<br />Resource groep verwijderen<br />Naam van resource groep wijzigen |
| **Geplande taak** | Taak maken<br />Taak wijzigen<br />Taak verwijderen<br />Taak uitvoeren |
| **Sessies** | Bericht<br />Sessie valideren |
| **Profiel** | Weer gave voor profiel gerichte opslag |
| **Opslag weergave** | Weergave |
| **vApp** | Virtuele machine toevoegen<br />Resource groep toewijzen<br />VApp toewijzen<br />Klonen<br />Maken<br />Verwijderen<br />Exporteren<br />Importeren<br />Verplaatsen<br />Uitschakelen<br />Inschakelen<br />Naam wijzigen<br />Onderbreken<br />Registratie ongedaan maken<br />OVF-omgeving weer geven<br />configuratie van vApp-toepassing<br />configuratie van vApp-exemplaar<br />configuratie van vApp managedBy<br />vApp-resource configuratie |
| **Virtuele machine** | Configuratie wijzigen<br />&#160;&#160;&#160;&#160;-adreslease ophalen<br />Bestaande schijf &#160;&#160;&#160;&#160;toevoegen<br />Nieuwe schijf &#160;&#160;&#160;&#160;toevoegen<br />&#160;&#160;&#160;&#160;apparaat toevoegen of verwijderen<br />Geavanceerde configuratie van &#160;&#160;&#160;&#160;<br />Aantal CPU-&#160;&#160;&#160;&#160;wijzigen<br />&#160;&#160;&#160;&#160;geheugen wijzigen<br />Instellingen &#160;&#160;&#160;&#160;wijzigen<br />Swapfile-plaatsing &#160;&#160;&#160;&#160;wijzigen<br />Resource &#160;&#160;&#160;&#160;wijzigen<br />USB-apparaat van host &#160;&#160;&#160;&#160;configureren<br />&#160;&#160;&#160;&#160;onbewerkt apparaat configureren<br />ManagedBy configureren &#160;&#160;&#160;&#160;<br />Verbindings instellingen &#160;&#160;&#160;&#160;weer geven<br />Virtuele schijf &#160;&#160;&#160;&#160;uitbreiden<br />Apparaatinstellingen &#160;&#160;&#160;&#160;wijzigen<br />Compatibiliteit van de query fout tolerantie &#160;&#160;&#160;&#160;<br />Bestanden &#160;&#160;&#160;&#160;oneigendom van de query<br />&#160;&#160;&#160;&#160;opnieuw laden vanaf paden<br />Schijf &#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;naam wijzigen<br />Gast gegevens &#160;&#160;&#160;&#160;opnieuw instellen<br />Aantekening &#160;&#160;&#160;&#160;instellen<br />&#160;&#160;&#160;&#160;schijf wijzigingen bijhouden uitschakelen<br />&#160;&#160;&#160;&#160;Fork-bovenliggend element in-/uitschakelen<br />Compatibiliteit met de upgrade van virtuele machines &#160;&#160;&#160;&#160;<br />Inventaris bewerken<br />&#160;&#160;&#160;&#160;maken op basis van bestaande<br />&#160;&#160;&#160;&#160;nieuwe maken<br />&#160;&#160;&#160;&#160;verplaatsen<br />&#160;&#160;&#160;&#160;registreren<br />&#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;registratie ongedaan maken<br />Gast bewerkingen<br />Wijziging van alias voor &#160;&#160;&#160;&#160;van gast bewerking<br />Alias query &#160;&#160;&#160;&#160;gast bewerking<br />Wijzigingen in de gast bewerking &#160;&#160;&#160;&#160;<br />Uitvoering van programma voor gast bewerking &#160;&#160;&#160;&#160;<br />Query's voor de gast bewerking &#160;&#160;&#160;&#160;<br />Interactie<br />Vraag &#160;&#160;&#160;&#160;beantwoorden<br />Back-upbewerking &#160;&#160;&#160;&#160;op virtuele machine<br />&#160;&#160;&#160;&#160;CD-media configureren<br />&#160;&#160;&#160;&#160;configureren van diskette media<br />&#160;&#160;&#160;&#160;apparaten verbinden<br />Interactie &#160;&#160;&#160;&#160;-console<br />Scherm opname &#160;&#160;&#160;&#160;maken<br />&#160;&#160;&#160;&#160;alle schijven defragmenteren<br />&#160;&#160;&#160;&#160;slepen en neerzetten<br />VIX-API voor het beheer van gast besturingssystemen &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;invoeren van USB-HID-scan codes<br />&#160;&#160;&#160;&#160;VMware-hulpprogram ma's installeren<br />&#160;&#160;&#160;&#160;onderbreken of onderbreken<br />&#160;&#160;&#160;&#160;wissen of verkleinen<br />&#160;&#160;&#160;&#160;uitschakelen<br />&#160;&#160;&#160;&#160;inschakelen<br />Sessie &#160;&#160;&#160;&#160;record op virtuele machine<br />&#160;&#160;&#160;&#160;sessie opnieuw afspelen op de virtuele machine<br />&#160;&#160;&#160;&#160;onderbreken<br />Fout tolerantie &#160;&#160;&#160;&#160;uitstellen<br />Testfailover &#160;&#160;&#160;&#160;<br />Secundaire VM &#160;&#160;&#160;&#160;test opnieuw starten<br />&#160;&#160;&#160;&#160;fout tolerantie uitschakelen<br />Fout tolerantie &#160;&#160;&#160;&#160;inschakelen<br />Inrichten<br />&#160;&#160;&#160;&#160;schijf toegang toestaan<br />Bestands toegang &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;alleen-lezen schijf toegang toestaan<br />Downloaden van virtuele machine &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;kloon sjabloon<br />Virtuele machine &#160;&#160;&#160;&#160;klonen<br />&#160;&#160;&#160;&#160;sjabloon maken op basis van de virtuele machine<br />Gast &#160;&#160;&#160;&#160;aanpassen<br />Sjabloon &#160;&#160;&#160;&#160;implementeren<br />&#160;&#160;&#160;&#160;markeren als sjabloon<br />Specificatie van aanpassing van &#160;&#160;&#160;&#160;wijzigen<br />&#160;&#160;&#160;&#160;promo veren van schijven<br />Specificaties van &#160;&#160;&#160;&#160;lezen<br />Serviceconfiguratie<br />Meldingen &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;polling van globale gebeurtenis meldingen toestaan<br />Service configuratie &#160;&#160;&#160;&#160;beheren<br />Service configuratie &#160;&#160;&#160;&#160;wijzigen<br />Configuraties van &#160;&#160;&#160;&#160;query service<br />Configuratie van &#160;&#160;&#160;&#160;-Lees service<br />Beheer van moment opnamen<br />Moment opname &#160;&#160;&#160;&#160;maken<br />Moment opname &#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;naam van moment opname wijzigen<br />&#160;&#160;&#160;&#160;moment opname herstellen<br />vSphere-replicatie<br />Replicatie &#160;&#160;&#160;&#160;configureren<br />Replicatie &#160;&#160;&#160;&#160;beheren<br />Replicatie &#160;&#160;&#160;&#160;controleren |
| **vService** | Afhankelijkheid maken<br />Afhankelijkheid vernietigen<br />Afhankelijkheids configuratie opnieuw configureren<br />Afhankelijkheid bijwerken |

## <a name="create-custom-roles-on-vcenter"></a>Aangepaste rollen maken op vCenter

De Azure VMware-oplossing ondersteunt het gebruik van aangepaste rollen met gelijke of minder bevoegdheden dan de CloudAdmin-rol. 

De rol CloudAdmin kan aangepaste rollen maken, wijzigen of verwijderen die bevoegdheden hebben die kleiner zijn dan of gelijk zijn aan de huidige rol. U kunt mogelijk rollen maken met meer bevoegdheden dan CloudAdmin, maar u kunt de rol niet toewijzen aan gebruikers of groepen of de rol verwijderen.

Om te voor komen dat er rollen worden gemaakt die niet kunnen worden toegewezen of verwijderd, wordt aanbevolen om de CloudAdmin-functie te klonen als basis voor het maken van nieuwe aangepaste rollen.

### <a name="create-a-custom-role"></a>Een aangepaste rol maken
1. Meld u aan bij vCenter met cloudadmin \@ vSphere. local of een gebruiker met de rol cloudadmin.
2. Ga naar de sectie configuratie van **functies** en selecteer **menu**  >  **beheer**  >  **Access Control**  >  **rollen**.
3. Selecteer de rol **CloudAdmin** en selecteer het **actie pictogram kloon van rol** .

   > [!NOTE] 
   > Kloon **de beheerdersrol** niet. Deze rol kan niet worden gebruikt en de aangepaste rol die is gemaakt, kan niet worden verwijderd door cloudadmin \@ vSphere. local.

4. Geef de gewenste naam voor de gekloonde rol op.
5. Voeg bevoegdheden toe of verwijder deze voor de rol en selecteer **OK**. De gekloonde rol moet nu worden weer gegeven in de lijst **rollen** .


### <a name="use-a-custom-role"></a>Een aangepaste rol gebruiken

1. Navigeer naar het object waarvoor de toegevoegde machtiging is vereist. Als u de machtiging bijvoorbeeld wilt Toep assen op een map, gaat u naar **menu**  >  **vm's en sjablonen**  >  **mapnaam**
1. Klik met de rechter muisknop op het object en selecteer **machtiging toevoegen**.
1. Selecteer in het venster **machtiging toevoegen** de id-bron in de vervolg keuzelijst van de **gebruiker** waar de groep of gebruiker kan worden gevonden.
1. Zoek naar de gebruiker of groep nadat u de id-bron hebt geselecteerd in het gedeelte **gebruiker** . 
1. Selecteer de rol die wordt toegepast op de gebruiker of groep.
1. Controleer zo nodig de **door geven aan kinderen** en selecteer **OK**.
   De toegevoegde machtiging wordt weer gegeven in de sectie **machtigingen** voor het object.

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van het toegangs beheer op basis van rollen voor Azure VMware-oplossing hebt behandeld, wilt u mogelijk meer informatie over:

- De details van elke bevoegdheid in de [VMware-product documentatie](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Hoe Azure VMware Solution bewaakt en herstelt persoonlijke Clouds](concepts-monitor-repair-private-cloud.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

