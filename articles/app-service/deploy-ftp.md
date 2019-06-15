---
title: Nasazení pomocí FTP/S – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci do služby Azure App Service pomocí FTP a FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: cephalin;dariac
ms.custom: seodec18
ms.openlocfilehash: db8445ec2b3dd8bdefa661d7f186e720c6fada09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130105"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Nasazení aplikace do Azure App Service pomocí FTP/S

V tomto článku se dozvíte, jak nasadit webovou aplikaci, back-endu mobilní aplikace nebo aplikaci API pomocí FTP a FTPS [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Koncový bod FTP/S pro aplikace je již aktivní. Žádná konfigurace je nutná pro povolení nasazení FTP/S.

## <a name="open-ftp-dashboard"></a>Otevřít řídicí panel FTP

V [webu Azure portal](https://portal.azure.com), otevřete v této aplikaci [stránka s materiály pro](../azure-resource-manager/manage-resources-portal.md#manage-resources).

Řídicí panel FTP, klikněte na tlačítko **Deployment Center** > **FTP** > **řídicí panel**.

![Otevřít řídicí panel FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Získání informací o připojení FTP

Na řídicím panelu FTP, klikněte na tlačítko **kopírování** kopírování přihlašovacích údajů pro koncový bod a aplikace FTPS.

![Kopírování informací FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Doporučuje se, že používáte **aplikace pověření** nasadit do vaší aplikace, protože je pro každou aplikaci jedinečné. Nicméně pokud klepnete na tlačítko **přihlašovací údaje uživatele**, můžete nastavit přihlašovací údaje na úrovni uživatele, které můžete použít pro přihlášení k FTP/S pro všechny aplikace služby App Service v rámci vašeho předplatného.

> [!NOTE]
> Ověřování FTP/FTPS koncový bod pomocí requirers přihlašovací údaje na úrovni uživatele s uživatelským jménem v následujícím formátu: 
>
>`<app-name>\<user-name>`
>
> Protože přihlašovací údaje na úrovni uživatele jsou propojeny s uživateli a konkrétní prostředek, uživatelské jméno musí být v tomto formátu pro přesměrování akce přihlásit ke koncovému bodu té správné aplikace.
>

## <a name="deploy-files-to-azure"></a>Nasazení souborů do Azure

1. Ze svého klienta FTP (například [sady Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/), nebo [WinSCP](https://winscp.net/index.php)), použijte informace o připojení, které jste shromáždili pro připojení k vaší aplikace.
2. Zkopírujte své soubory a příslušnou adresářovou strukturu do [ **/site/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) v Azure (nebo **/site/wwwroot/App_Data/úlohy/** adresář pro webové úlohy).
3. Přejděte na adresu URL vaší aplikace k ověření, že aplikace běží správně. 

> [!NOTE] 
> Na rozdíl od [nasazení z Gitu](deploy-local-git.md), FTP nasazení nepodporuje následující automatizace nasazení: 
>
> - Obnoví závislostí (jako je NuGet, NPM, PIP a autora automatizace)
> - kompilace .NET binárních souborů
> - generování souboru Web.config (tady je [příkladu Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generovat tyto nezbytné soubory ručně na místním počítači a potom je nasadit společně s vaší aplikace.
>

## <a name="enforce-ftps"></a>Vynutit FTPS

Pro zvýšení zabezpečení byste měli povolit FTP přes protokol SSL jen. Pokud nepoužíváte FTP nasazení, můžete zakázat také FTP a FTPS.

Na stránce prostředků vaší aplikace v [webu Azure portal](https://portal.azure.com)vyberte **nastavení aplikace** v levém navigačním panelu.

Chcete-li zakázat nešifrované FTP, vyberte **FTPS pouze**. Chcete-li zcela zakázat FTP a FTPS, vyberte **zakázat**. Jakmile budete hotoví, klikněte na **Uložit**. Pokud používáte **FTPS pouze** musí vynucení protokolu TLS 1.2 nebo vyšší tak, že přejdete na **nastavení SSL** okno vaší webové aplikace. Protokol TLS 1.0 a 1.1 se nepodporují **FTPS pouze**.

![Zakázat FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Pro nasazení pomocí FTP [rozhraní příkazového řádku Azure](/cli/azure), naleznete v tématu [vytvoření webové aplikace a nasazení souborů s využitím protokolu FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

Pro nasazení pomocí FTP [prostředí Azure PowerShell](/cli/azure), naleznete v tématu [nahrání souborů do webové aplikace pomocí FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Řešení potíží s nasazení FTP

- [Jak můžete řešit potíže s FTP nasazení?](#how-can-i-troubleshoot-ftp-deployment)
- [Nemůžu se připojit přes protokol FTP a publikovat kód. Jak lze problém vyřešit?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak se můžete připojit k serveru FTP ve službě Azure App Service prostřednictvím pasivní režim?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak můžete řešit potíže s FTP nasazení?

Prvním krokem pro řešení potíží s nasazením serveru FTP je pak izolovat o problém s nasazením z chybu modulu runtime aplikace.

Problém nasazení obvykle výsledkem žádné soubory nebo nesprávné soubory nasadit do vaší aplikace. Řešení potíží s prošetření vašeho nasazení FTP nebo výběrem cestu k alternativní nasazení (například správy zdrojového kódu).

Aplikací potíže s modulem runtime obvykle vytváří správnou sadu souborů, které jsou nasazené do vaší aplikace, ale chování nesprávné aplikace. Řešení potíží s soustředit na kód chování za běhu a zkoumání selhání konkrétní cesty.

Problém nasazení nebo modul runtime, zjistíte v [nasazení vs. problémy za běhu](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Můžu nejste schopni FTP a publikování vlastní kód. Jak lze problém vyřešit?
Zkontrolujte, zda jste zadali správný název hostitele a [pověření](#open-ftp-dashboard). Zkontrolujte také, že následující porty FTP na vašem počítači nejsou blokovány bránou firewall:

- Port připojení řízení FTP: 21
- Port připojení dat FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak se můžete připojit k serveru FTP ve službě Azure App Service prostřednictvím pasivní režim?
Azure App Service podporuje připojení přes aktivní a pasivní režim. Pasivní režim je upřednostňované, protože vaše nasazení. virtuální počítače jsou obvykle za bránou firewall (v operačním systému nebo jako součást domácí nebo firemní sítě). Najdete v článku [příkladu v dokumentaci ke službě WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Další postup

Pro pokročilejší scénáře nasazení, zkuste [nasazení do Azure pomocí Gitu](deploy-local-git.md). Nasazení z Gitu do Azure umožňuje správu verzí, obnovení balíčku, nástroj MSBuild a další.

## <a name="more-resources"></a>Další materiály

* [Přihlašovací údaje pro nasazení služby Azure App Service](deploy-configure-credentials.md)
