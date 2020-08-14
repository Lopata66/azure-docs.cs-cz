---
title: Správa Azure Data Box/Azure Data Box Heavy pomocí místního webového uživatelského rozhraní
description: Popisuje způsob použití místního webového uživatelského rozhraní pro správu Data Box a Data Box Heavy zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/10/2020
ms.author: alkohli
ms.openlocfilehash: 7cac14708adecbdf3c809e3a9656d25c727d80e3
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206162"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Použití místního webového uživatelského rozhraní ke správě Data Box a Data Box Heavy

Tento článek popisuje některé úlohy konfigurace a správy, které je možné provádět na Data Box a Data Box Heavych zařízeních. Zařízení Data Box a Data Box Heavy můžete spravovat prostřednictvím uživatelského rozhraní Azure Portal a místního webového uživatelského rozhraní pro dané zařízení. Tento článek se zaměřuje na úlohy, které můžete provádět pomocí místního webového uživatelského rozhraní.

Místní webové uživatelské rozhraní pro Data Box a Data Box Heavy se používá pro počáteční konfiguraci zařízení. Pomocí místního webového uživatelského rozhraní můžete také vypnout nebo restartovat zařízení, spustit diagnostické testy, aktualizovat software, zobrazit kopie protokolů a vygenerovat balíček protokolu pro podpora Microsoftu. Na zařízení Data Box Heavy se dvěma nezávislými uzly můžete přistupovat ke dvěma samostatným místním webovým uživatelská rozhraníám, které odpovídají každému uzlu zařízení.

Tento článek obsahuje následující návody:

- Generování balíčku pro podporu
- Vypnutí nebo restartování zařízení
- Stažení kusovníku nebo souborů manifestu
- Zobrazení dostupné kapacity zařízení
- Přeskočení ověření kontrolního součtu

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>Generování balíčku pro podporu

Pokud budete mít se zařízením jakékoliv problémy, můžete vytvořit ze systémových protokolů balíček pro podporu. Podpora Microsoftu používá tento balíček k řešení příslušných potíží. Chcete-li vytvořit balíček pro podporu, proveďte následující kroky:

1. V místním webovém uživatelském rozhraní klikněte na **kontaktovat podporu** a vyberte **vytvořit balíček pro podporu**.

    ![Vytvoření balíčku pro podporu 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Shromáždí se položky balíčku pro podporu. Tato operace trvá několik minut.

    ![Vytvoření balíčku pro podporu 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Po dokončení vytváření balíčku podpory vyberte **Stáhnout balíček pro podporu**.

    ![Vytvoření balíčku pro podporu 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Přejděte k umístění pro stahování a vyberte ho. Otevřete složku a zobrazte obsah.

    ![Vytvoření balíčku pro podporu 5](media/data-box-local-web-ui-admin/create-support-package-5.png)

## <a name="shut-down-or-restart-your-device"></a>Vypnutí nebo restartování zařízení

Zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme před restartováním přepnout sdílené složky na hostiteli a potom na zařízení do offline režimu. Tím se minimalizuje jakákoli možnost poškození dat. Při vypnutí zařízení zkontrolujte, že se nekopírují data.

Pokud chcete zařízení vypnout, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Vyberte **vypnout**.

    ![Vypnutí Data Boxu 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte výběrem **OK** .

    ![Vypnutí Data Boxu 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Zařízení po vypnutí zapněte tlačítkem napájení na předním panelu.

Při restartování Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Vyberte **restartovat**.

    ![Restartování Data Boxu 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte výběrem **OK** .

   Zařízení se vypne a restartuje.

## <a name="download-bom-or-manifest-files"></a>Stažení souboru kusovníku nebo manifestu

KUSOVNÍK nebo soubory manifestu obsahují seznam souborů, které jsou zkopírovány do Data Box nebo Data Box Heavy. Tyto soubory jsou vygenerovány pro pořadí importu při přípravě zařízení k odeslání.

Než začnete, postupujte podle těchto kroků a Stáhněte soubory kusovníku nebo manifestu pro vaše pořadí importu:

1. Přejít k místnímu webovému uživatelskému rozhraní pro vaše zařízení. Ověřte, že zařízení dokončilo **Příprava k odeslání** krok. Až se dokončí příprava zařízení, zobrazí se stav zařízení jako **připravený k odeslání**.

    ![Zařízení připravené k dodání](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Vyberte **Stáhnout seznam souborů** a Stáhněte si seznam souborů, které jste zkopírovali v data box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. V Průzkumníkovi souborů uvidíte, že se v závislosti na protokolu používaném pro připojení k zařízení a používaném typu Azure Storage generují samostatné seznamy souborů.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Soubory pro typ úložiště a protokol připojení](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   Následující tabulka mapuje názvy souborů na typ Azure Storage a použitý protokol připojení.

    |Název souboru  |Typ Azure Storage  |Použitý protokol připojení |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Objekty blob bloku         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Objekty blob stránky         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Soubory Azure         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Objekty blob stránky         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Objekty blob bloku         |REST         |

Pomocí tohoto seznamu ověříte soubory nahrané do Azure Storage účtu poté, co se Data Box vrátí do datacentra Azure. Ukázkový soubor manifestu je uveden níže.

> [!NOTE]
> Na Data Box Heavy jsou k dispozici dvě sady souborů (soubory KUSOVNÍKŮ) odpovídající dvěma uzlům na zařízení.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Tento soubor obsahuje seznam všech souborů, které byly zkopírovány na Data Box nebo Data Box Heavy. V tomto souboru se hodnota *crc64* vztahuje k kontrolnímu součtu vygenerovanému pro odpovídající soubor.

## <a name="view-available-capacity-of-the-device"></a>Zobrazení dostupné kapacity zařízení

Dostupnou a využitou kapacitu zařízení můžete zobrazit na řídicím panelu zařízení.

1. V místním webovém uživatelském rozhraní přejděte na **Zobrazit řídicí panel**.
2. V části **Připojit a kopírovat** se zobrazí volné a využité místo zařízení.

    ![Zobrazení dostupné kapacity](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Přeskočení ověření kontrolního součtu

Při přípravě na dodávání se pro vaše data generují kontrolní součty ve výchozím nastavení. Ve výjimečných případech v závislosti na datovém typu (malé velikosti souborů) může být výkon pomalý. V takových případech můžete kontrolní součet přeskočit.

Výpočet kontrolního součtu během Příprava na odeslání je proveden pouze pro objednávky importu, nikoli pro objednávky exportu. 

Důrazně doporučujeme kontrolní součet nezakazovat, pokud to výrazně neovlivňuje výkon.

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení, přejít na **Nastavení**.

    ![Zakázání kontrolních součtů](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Zakázání** ověření kontrolního součtu
3. Vyberte **Použít**.

> [!NOTE]
> Možnost přeskočit výpočet kontrolního součtu je k dispozici, pouze pokud je Azure Data Box odemčená. Tato možnost se nezobrazuje, když je zařízení zamčené.

## <a name="enable-smb-signing"></a>Povolit podepisování SMB

Podepisování SMB (Server Message Block) je funkce, prostřednictvím které se komunikace pomocí protokolu SMB může digitálně podepsat na úrovni paketů. Toto podepisování zabraňuje útokům, které mění pakety SMB při přenosu.

Další informace týkající se podepisování protokolu SMB najdete v tématu [Přehled podepisování bloků zpráv serveru](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Povolení podepisování SMB v zařízení Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

    ![Otevřít nastavení](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** Podepisování SMB.

    ![Povolit podepisování SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Vyberte **Použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="enable-tls-11"></a>Povolit TLS 1,1

Ve výchozím nastavení používá Azure Data Box k šifrování protokol TLS (Transport Layer Security) 1,2, protože je bezpečnější než TSL 1,1. Pokud ale vy nebo vaši klienti používáte prohlížeč pro přístup k datům, která nepodporují protokol TLS 1,2, můžete povolit TLS 1,1.

Další informace související s protokolem TLS najdete v tématu [Azure Data box Gateway Security](../databox-online/data-box-gateway-security.md).

Povolení TLS 1,1 ve vašem zařízení Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

    ![Otevřít nastavení](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** TLS 1,1.

    ![Povolit TLS 1,1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Vyberte **Použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="next-steps"></a>Další kroky

- Naučte se [spravovat data box a data box Heavy prostřednictvím Azure Portal](data-box-portal-admin.md).
