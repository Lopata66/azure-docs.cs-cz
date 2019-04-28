---
title: Správa zón DNS v Azure DNS – PowerShell | Dokumentace Microsoftu
description: Můžete spravovat zóny DNS pomocí Azure Powershellu. Tento článek popisuje, jak aktualizovat, odstranit a vytvářet zóny DNS v Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: 1ef44c16a8ae3b6254a6cea252501b72ddb24a5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293618"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Správa zón DNS pomocí Powershellu

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

V tomto článku se dozvíte, jak spravovat zóny DNS pomocí Azure Powershellu. Můžete také spravovat zóny DNS pomocí napříč platformami [rozhraní příkazového řádku Azure](dns-operations-dnszones-cli.md) nebo na webu Azure portal.

Tato příručka konkrétně zabývá veřejných zón DNS. Informace o použití Azure Powershellu ke správě privátní zóny v Azure DNS najdete v tématu [Začínáme s Azure DNS Private Zones pomocí Azure Powershellu](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzureRmDnsZone`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Následující příklad ukazuje, jak vytvořit zónu DNS se dvěma [značky Azure Resource Manageru](dns-zones-records.md#tags), *project = demo* a *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS teď podporuje také privátní zóny DNS (aktuálně ve verzi Public Preview).  Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí PowerShellu](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Získat zóny DNS

Pokud chcete načíst zónu DNS, použijte `Get-AzureRmDnsZone` rutiny. Tato operace vrátí objekt zóny DNS odpovídá existující zónu v Azure DNS. Objekt obsahuje údaje o zóně (jako je počet sad záznamů), ale neobsahuje sady záznamů, sami (viz `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Výpis zón DNS

Vynecháním názvu zóny v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí rutiny `Set-AzureRmDnsZone`. Tato rutina neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Vlastnosti zóny s možností zápisu jsou aktuálně omezené na [Azure Resource Manageru 'značky' pro prostředek zóny](dns-zones-records.md#tags).

Použijte jednu z následujících dvou způsobů, jak aktualizace zóny DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Určení zóny pomocí zóny a skupinou prostředků

Tento přístup nahradí existující zónu značky zadané hodnoty.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Tento přístup zkopíruje existující objekt zóny, upravit značky a pak potvrdí změny. Tímto způsobem je možné zachovat existující značky.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Při použití `Set-AzureRmDnsZone` pomocí objektu $zone [Etag kontroluje](dns-zones-records.md#etags) se používají k zajištění souběžných změn se nepřepíšou. Můžete použít nepovinnou `-Overwrite` přepínač tak, aby potlačit tyto kontroly.

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí `Remove-AzureRmDnsZone` rutiny.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).


Zónu DNS můžete odstranit některým z následujících dvou způsobů:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Určení zóny pomocí názvu zóny a názvu skupiny prostředků

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Zónu k odstranění můžete určit pomocí objektu `$zone` vráceného rutinou `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Místo předání jako parametru je možné objekt zóny předat také rourou:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Stejně jako u `Set-AzureRmDnsZone`, určení zóny pomocí `$zone` objektu umožňuje Značka Etag kontroly pro zajištění souběžných změny nebudou odstraněny. Použití `-Overwrite` přepínač tak, aby potlačit tyto kontroly.

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` a `Remove-AzureRmDnsZone` podporují výzvy k potvrzení.

Rutiny `New-AzureRmDnsZone` a `Set-AzureRmDnsZone` zobrazí výzvu k potvrzení v případě, že má proměnná předvolby PowerShellu `$ConfirmPreference` hodnotu `Medium` nebo nižší. Kvůli potenciálně velkému dopadu odstranění zóny DNS zobrazí rutina `Remove-AzureRmDnsZone` výzvu k potvrzení v případě, že má proměnná PowerShellu `$ConfirmPreference` jinou hodnotu než `None`.

Vzhledem k tomu, že výchozí hodnota proměnné `$ConfirmPreference` je `High`, ve výchozím nastavení zobrazí výzvu k potvrzení pouze rutina `Remove-AzureRmDnsZone`.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí.

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [spravovat sady záznamů a záznamy](dns-operations-recordsets.md) ve vaší zóně DNS.
<br>
Zjistěte, jak [delegování domény do Azure DNS](dns-domain-delegation.md).
<br>
Zkontrolujte [referenční dokumentace k Azure DNS Powershellu](/powershell/module/azurerm.dns).

