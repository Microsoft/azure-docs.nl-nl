---
title: Update installeren op Azure Stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u updates toepast met de Azure Portal en de lokale web-UI voor Azure Stack Edge Pro GPU-apparaat en het Kubernetes-cluster op het apparaat
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 60c6d0b7c983aefbca3aec65a3f6562edb1d56ef
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956176"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Uw Azure Stack Edge Pro GPU bijwerken 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel worden de stappen beschreven die nodig zijn om updates te installeren op uw Azure Stack Edge Pro met GPU via de lokale webgebruikersinterface en via de Azure Portal. U past de software-updates of hotfixes toe om uw Azure Stack Edge Pro-apparaat en het bijbehorende Kubernetes-cluster op het apparaat up-to-date te houden.

De in dit artikel beschreven procedure is uitgevoerd met een andere versie van de software, maar het proces blijft hetzelfde voor de huidige software versie.

> [!IMPORTANT]
> - Update **2103** is de huidige update en komt overeen met:
>   - Software versie van apparaat- **2.2.1540.2890**
>   - Kubernetes-Server versie- **v 1.17.3**
>   - IoT Edge-versie: **0.1.0-beta13**
>   - Versie van GPU-stuur programma: **460.32.03**
>   - CUDA-versie: **11,2**
>    
>    Ga naar [release opmerkingen](azure-stack-edge-gpu-2103-release-notes.md)voor meer informatie over wat er nieuw is in deze update.
> - Om 2103-update toe te passen, moet op uw apparaat 2010 worden uitgevoerd. Als u niet de mini maal ondersteunde versie gebruikt, ziet u deze fout: *Update pakket kan niet worden geïnstalleerd omdat niet aan de afhankelijkheden wordt voldaan*.
> - Als u een update of hotfix installeert, wordt het apparaat opnieuw opgestart. Deze update bevat de software-updates van het apparaat en de Kubernetes-updates. Gezien de Azure Stack Edge Pro is een apparaat met één knoop punt, worden alle I/O-bewerkingen onderbroken en wordt het apparaat een downtime van Maxi maal 1,5 uur voor de update.

Als u updates op uw apparaat wilt installeren, moet u eerst de locatie van de update server configureren. Nadat de update server is geconfigureerd, kunt u de updates Toep assen via de Azure Portal gebruikers interface of de lokale webgebruikersinterface.

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

## <a name="configure-update-server"></a>Update server configureren

1. Ga in de lokale web-UI naar **configuratie**  >  **Update server**.
   
    ![Updates configureren 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. In **Selecteer update server type** in de vervolg keuzelijst kiest u van Microsoft Update server (standaard) of Windows Server Update Services.  
   
    Als u een update uitvoert vanuit de Windows Server Update Services, geeft u de server-URI op. De-server op die URI implementeert de updates op alle apparaten die zijn verbonden met deze server.

    ![Updates configureren 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    De WSUS-server wordt gebruikt om updates te beheren en te distribueren via een beheer console. Een WSUS-server kan ook de update bron zijn voor andere WSUS-servers binnen de organisatie. De WSUS-server die fungeert als updatebron wordt een upstream-server genoemd. In een WSUS-implementatie moet ten minste één WSUS-server in uw netwerk verbinding kunnen maken met Microsoft Update om beschik bare Update gegevens te verkrijgen. Als beheerder kunt u bepalen op basis van netwerk beveiliging en-configuratie-het aantal andere WSUS-servers die rechtstreeks verbinding maken met Microsoft Update.
    
    Ga voor meer informatie naar [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U wordt aangeraden updates te installeren via de Azure Portal. Het apparaat scant eenmaal per dag automatisch op updates. Zodra de updates beschikbaar zijn, ziet u een melding in de portal. Vervolgens kunt u de updates downloaden en installeren.

> [!NOTE]
> Zorg ervoor dat het apparaat in orde is en dat de status wordt weer gegeven als **online** voordat u doorgaat met de installatie van de updates.

1. Wanneer de updates beschikbaar zijn voor uw apparaat, ziet u een melding. Selecteer de melding of van de bovenste opdracht balk, **werk het apparaat** bij. Hiermee kunt u software-updates voor apparaten Toep assen.

    ![Software versie na update](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Controleer op de Blade **apparaat bijwerken** of u de licentie voorwaarden hebt gelezen die zijn gekoppeld aan nieuwe functies in de release opmerkingen.

    U kunt ervoor kiezen om de updates te **downloaden en te installeren** of alleen de updates te **downloaden** . Vervolgens kunt u ervoor kiezen om deze updates later te installeren.

    ![Software versie na update 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Als u de updates wilt downloaden en installeren, controleert u de optie dat updates automatisch worden geïnstalleerd nadat het downloaden is voltooid.

    ![Software versie na update 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Het downloaden van updates wordt gestart. U ziet een melding dat de down load wordt uitgevoerd.

    ![Software versie na update 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Er wordt ook een meldings banner weer gegeven in de Azure Portal. Dit geeft de voortgang van de down load aan.

    ![Software versie na update 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    U kunt deze melding selecteren of het **apparaat bijwerken** selecteren om de gedetailleerde status van de update te bekijken.

    ![Software versie na update 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Nadat het downloaden is voltooid, wordt de meldings banner bijgewerkt om aan te geven dat de voltooiing is voltooid. Als u ervoor hebt gekozen om de updates te downloaden en te installeren, wordt de installatie automatisch gestart.

    ![Software versie na update 7](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Als u ervoor hebt gekozen om alleen updates te downloaden, selecteert u de melding om de Blade updates van het **apparaat** te openen. Selecteer **Installeren**.
  
    ![Software versie na update 8](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. U ziet een melding dat de installatie wordt uitgevoerd.

    ![Software versie na update 9](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    In de portal wordt ook een informatieve waarschuwing weer gegeven om aan te geven dat de installatie wordt uitgevoerd. Het apparaat wordt offline gezet en bevindt zich in de onderhouds modus.
   
    ![Software versie na update 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Aangezien dit een apparaat met één knoop punt is, wordt het apparaat opnieuw opgestart nadat de updates zijn geïnstalleerd. De kritieke waarschuwing tijdens het opnieuw opstarten geeft aan dat de heartbeat van het apparaat verloren is gegaan.

    ![Software versie na update 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Selecteer de waarschuwing om de bijbehorende gebeurtenis te bekijken.
    
    ![Software versie na update 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Als u na het opnieuw opstarten het **apparaat bijwerken** op de bovenste opdracht balk selecteert, ziet u de voortgang van de updates.   

8. De status van het apparaat wordt **online** bijgewerkt nadat de updates zijn geïnstalleerd. 

    ![Software versie na update 13](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    Selecteer op de bovenste opdracht balk de optie updates van het **apparaat**. Controleer of de update is geïnstalleerd en of de versie van de apparaatsoftware overeenkomt met die.

    ![Software versie na update 14](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)


Zodra de Kubernetes-software is geïnstalleerd, verdwijnt de banner melding. Uw apparaat heeft nu de nieuwste versie van de apparaatsoftware en Kubernetes.


## <a name="use-the-local-web-ui"></a>De lokale web-UI gebruiken

Er zijn twee stappen voor het gebruik van de lokale web-UI:

* De update of hotfix downloaden
* De update of hotfix installeren

Deze stappen worden afzonderlijk in detail beschreven in de volgende gedeelten.


### <a name="download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

Voer de volgende stappen uit om de update te downloaden. U kunt de update downloaden van de door micro soft geleverde locatie of vanuit de Microsoft Update catalogus.


Voer de volgende stappen uit om de update te downloaden uit de Microsoft Update catalogus.

1. Start de browser en ga naar [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Catalogus doorzoeken](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Voer in het zoekvak van de catalogus van Microsoft Update het Knowledge Base-nummer (KB) in van de hotfix of de voor waarden voor de update die u wilt downloaden. Voer bijvoorbeeld **Azure stack Edge Pro** in en klik vervolgens op **zoeken**.
   
    De update vermelding wordt weer gegeven als **Azure stack Edge update 2103**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Selecteer **Downloaden**. Er zijn twee pakketten die moeten worden gedownload: KB 4613486 en KB 46134867 die overeenkomen met de software-updates van het apparaat (*SoftwareUpdatePackage.exe*) en Kubernetes (*Kubernetes_Package.exe*) respectievelijk. Down load de pakketten naar een map op het lokale systeem. U kunt de map ook kopiëren naar een netwerk share die bereikbaar is vanaf het apparaat.

### <a name="install-the-update-or-the-hotfix"></a>De update of hotfix installeren

Voordat u de update of hotfix installeert, moet u het volgende doen:

 - U beschikt over de update of de hotfix is lokaal op uw host gedownload of toegankelijk via een netwerk share.
 - De apparaatstatus is in orde, zoals wordt weer gegeven op de pagina **overzicht** van de lokale webgebruikersinterface.

   ![apparaat bijwerken](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Het volt ooien van deze procedure duurt ongeveer 20 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

1. Ga in de lokale web-UI naar **onderhouds**  >  **Software-update**. Noteer de software versie die u uitvoert. 
   
   ![apparaat 2 bijwerken](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Geef het pad op naar het update bestand. U kunt ook naar het installatie bestand van de update bladeren als u deze op een netwerk share plaatst. Selecteer het software-update bestand met *SoftwareUpdatePackage.exe* achtervoegsel.

   ![apparaat 3 bijwerken](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Selecteer **Toepassen**.

   ![apparaat bijwerken 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Als u om bevestiging wordt gevraagd, selecteert u **Ja** om door te gaan. Gezien het apparaat is een apparaat met één knoop punt, nadat de update is toegepast, wordt het apparaat opnieuw opgestart en is er uitval tijd. 
   
   ![apparaat 5 bijwerken](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikers interface is niet toegankelijk in deze duur.
   
6. Wanneer het opnieuw opstarten is voltooid, wordt u naar de **aanmeldings** pagina geleid. Als u wilt controleren of de software van het apparaat is bijgewerkt, gaat u naar de **onderhouds**  >  **Software-update** in de lokale web-UI. Voor de huidige versie moet de weer gegeven software versie **Azure stack Edge 2103** zijn. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. U gaat nu de versie van de Kubernetes-software bijwerken. Herhaal de bovenstaande stappen. Geef een pad naar het Kubernetes-update bestand met het achtervoegsel van de *Kubernetes_Package.exe* op.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Selecteer **Update Toep assen**.

   ![apparaat 7 bijwerken](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Als u om bevestiging wordt gevraagd, selecteert u **Ja** om door te gaan.

10. Nadat de Kubernetes-update is geïnstalleerd, is er geen wijziging in de weer gegeven software in de **onderhouds**  >  **Software-update**.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw Azure stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).