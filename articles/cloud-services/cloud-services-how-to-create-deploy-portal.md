---
title: Jak vytvořit a nasadit cloudovou službu | Microsoft Docs
description: Naučte se vytvářet a nasazovat cloudovou službu pomocí Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 63cf864a3f3b92728ad613ac45542bdbce2c9858
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811334"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak vytvořit a nasadit cloudovou službu
Azure Portal poskytuje dva způsoby, jak vytvořit a nasadit cloudovou službu: *rychlé vytvoření* a *vlastní vytvoření*.

Tento článek vysvětluje, jak pomocí metody rychlého vytvoření vytvořit novou cloudovou službu a pak pomocí **nahrávání** nahrát a nasadit balíček cloudové služby v Azure. Když použijete tuto metodu, Azure Portal zpřístupňuje dostupné praktické odkazy pro dokončení všech požadavků. Pokud jste připravení nasadit cloudovou službu při jejím vytváření, můžete současně použít možnost vlastní vytvoření.

> [!NOTE]
> Pokud máte v úmyslu publikovat cloudovou službu z Azure DevOps, použijte rychlé vytvoření a pak nastavte publikování Azure DevOps z rychlého startu Azure nebo řídicího panelu. Další informace najdete v tématu [průběžné doručování do Azure pomocí Azure DevOps][TFSTutorialForCloudService], případně v nápovědě pro stránku **rychlé zprovoznění** .
>
>

## <a name="concepts"></a>Koncepty
K nasazení aplikace jako cloudové služby v Azure se vyžadují tři komponenty:

* **Definice služby**  
  Definiční soubor cloudové služby (. csdef) definuje model služby, včetně počtu rolí.
* **Konfigurace služby**  
  Konfigurační soubor cloudové služby (. cscfg) poskytuje nastavení konfigurace pro cloudovou službu a jednotlivé role, včetně počtu instancí rolí.
* **Balíček služby**  
  Balíček služby (. cspkg) obsahuje kód a konfiguraci aplikace a soubor definice služby.

Další informace o těchto a o tom, jak vytvořit balíček, najdete [tady](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Příprava aplikace
Než budete moct nasadit cloudovou službu, musíte vytvořit balíček cloudové služby (. cspkg) z kódu aplikace a konfiguračního souboru cloudové služby (. cscfg). Sada Azure SDK poskytuje nástroje pro přípravu požadovaných souborů nasazení. Sadu SDK můžete nainstalovat ze stránky [Azure downloads](https://azure.microsoft.com/downloads/) v jazyce, ve kterém dáváte přednost vývoji kódu aplikace.

Před exportem balíčku služby vyžadují tři funkce cloudové služby speciální konfigurace:

* Pokud chcete nasadit cloudovou službu, která používá protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL) pro šifrování dat, [nakonfigurujte aplikaci](cloud-services-configure-ssl-certificate-portal.md#modify) pro TLS.
* Pokud chcete nakonfigurovat připojení vzdálené plochy k instancím rolí, [nakonfigurujte role](cloud-services-role-enable-remote-desktop-new-portal.md) pro vzdálenou plochu.
* Pokud chcete pro cloudovou službu nakonfigurovat podrobné monitorování, povolte pro cloudovou službu Azure Diagnostics. *Minimální monitorování* (výchozí úroveň monitorování) využívá čítače výkonu shromážděné z hostitelských operačních systémů pro instance rolí (virtuální počítače). *Podrobné monitorování* shromažďuje další metriky založené na datech výkonu v rámci instancí rolí a umožňuje tak užší analýzu problémů, ke kterým dojde během zpracování aplikace. Postup povolení Azure Diagnostics najdete v tématu [Povolení diagnostiky v Azure](cloud-services-dotnet-diagnostics.md).

Chcete-li vytvořit cloudovou službu s nasazením webových rolí nebo rolí pracovního procesu, je nutné [vytvořit balíček služby](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Než začnete
* Pokud jste sadu Azure SDK nenainstalovali, klikněte na **nainstalovat sadu Azure SDK** , otevřete [stránku Azure downloads](https://azure.microsoft.com/downloads/)a Stáhněte si sadu SDK pro jazyk, ve kterém dáváte přednost vývoji kódu. (Budete mít příležitost k tomu později.)
* Pokud nějaké instance role vyžadují certifikát, vytvořte certifikáty. Cloud Services vyžadují soubor. pfx s privátním klíčem. Certifikáty můžete do Azure nahrát při vytváření a nasazení cloudové služby.

## <a name="create-and-deploy"></a>Vytváření a nasazování
1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Klikněte na **vytvořit prostředek > COMPUTE**, přejděte dolů na **cloudovou službu**a klikněte na ni.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. V podokně nová **cloudová služba** zadejte hodnotu pro **název DNS**.
4. Vytvořte novou **skupinu prostředků** nebo vyberte existující.
5. Vyberte **umístění**.
6. Klikněte na **balíček**. Tím se otevře podokno **nahrát balíček** . Vyplňte požadovaná pole. Pokud některá z vašich rolí obsahuje jednu instanci, zajistěte nasazení i v případě, že je vybraná **jedna nebo víc rolí** , které obsahují jednu instanci.
7. Ujistěte se, že je vybraná možnost **Spustit nasazení** .
8. Klikněte na tlačítko **OK** , čímž se zavře podokno **nahrát balíček** .
9. Pokud nemáte žádné certifikáty, které by se měly přidat, klikněte na **vytvořit**.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Odeslat certifikát
Pokud byl balíček pro nasazení [nakonfigurovaný tak, aby používal certifikáty](cloud-services-configure-ssl-certificate-portal.md#modify), můžete ho teď nahrát.

1. Vyberte **certifikáty**a v podokně **Přidat certifikáty** vyberte soubor. pfx certifikátu TLS/SSL a pak zadejte **heslo** pro certifikát.
2. Klikněte na tlačítko **připojit certifikát**a potom v podokně **Přidat certifikáty** klikněte na tlačítko **OK** .
3. V podokně **cloudová služba** klikněte na **vytvořit** . Až nasazení dosáhne stavu **připraveno** , můžete přejít k dalším krokům.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ověřte, že nasazení bylo úspěšně dokončeno.
1. Klikněte na instanci cloudové služby.

    Stav by měl ukazovat na to, že je služba **spuštěná**.
2. V části **základy**klikněte na **adresu URL webu** a otevřete tak cloudovou službu ve webovém prohlížeči.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md)
* Nakonfigurujte [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte svou cloudovou službu](cloud-services-how-to-manage-portal.md).
* Nakonfigurujte [certifikáty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



