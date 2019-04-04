---
title: Ověření úlohy Azure Stream Analytics k Azure Data Lake Storage Gen1 výstupu
description: Tento článek popisuje způsob použití spravované identity k ověření vaší úlohy Azure Stream Analytics k Azure Data Lake Storage Gen1 výstup.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 994ccf292a4215624d4222fe13ca9ac25c863368
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895862"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Ověření Stream Analytics pro Azure Data Lake Storage Gen1 pomocí spravované identity

Azure Stream Analytics podporuje spravovanou identitu ověřování pomocí Azure Data Lake Storage (ADLS) Gen1 výstup. Identita je spravovaná aplikace v Azure Active Directory, který představuje dané úlohy Stream Analytics a slouží k ověřování na cílový prostředek. Spravované identity eliminovat omezení metody ověřování založené na uživatelích, jako by bylo nutné donutit z důvodu změny hesla nebo uživatel token platnosti, ke kterým dochází vždy po 90 dnech. Kromě toho spravované identity usnadňující automatizaci nasazení úlohy Stream Analytics, která výstup do Azure Data Lake Storage Gen1.

Tento článek popisuje tři způsoby, jak povolit spravovanou identitu pro úlohy Azure Stream Analytics, jejichž výstupem jsou do Azure Data Lake Storage Gen1 prostřednictvím webu Azure portal, nasazení šablony Azure Resource Manageru a Azure Stream Analytics tools for Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>portál Azure

1. Začněte tím, že vytvoříte novou úlohu Stream Analytics nebo tak, že otevřete existující projekt na webu Azure portal. V panelu nabídky na levé straně obrazovky vyberte **Identity spravované** umístěna ve složce **konfigurovat**.

   ![Konfigurace Stream Analytics, spravované identity](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Vyberte **použijte systém přiřadil Identity spravované** z okna, která se zobrazí na pravé straně. Klikněte na tlačítko **Uložit** instančnímu objektu služby pro identifikaci úlohy Stream Analytics v Azure Active Directory. Azure bude spravovat životní cyklus nově vytvořený identity. Při odstranění úlohy Stream Analytics přidružený identity (to znamená, instanční objekt) Azure automaticky odstraní.

   Při uložení konfigurace ID objektu (OID) objektu služby, které je uvedené jako ID objektu zabezpečení, jak je znázorněno níže:

   ![ID instančního objektu Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Instanční objekt služby má stejný název jako úloha Stream Analytics. Například, pokud je název vaší úlohy **MyASAJob**, název se vytvořil objekt služby je také **MyASAJob**.

3. V okně výstupu vlastnosti výstupní jímky ADLS Gen1, klikněte na rozevírací seznam a vyberte režim ověřování ** Identity spravované **.

4. Vyplňte zbývající vlastnosti nástroje. Další informace o vytváření výstup ADLS najdete v tématu [vytvořit výstupní Data lake Store pomocí stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). Až budete hotovi, klikněte na tlačítko **Uložit**.

   ![Konfigurace úložiště Azure Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Přejděte na stránku přehled vaší ADLS Gen1 a klikněte na **Průzkumník dat**.

   ![Přehled služby Data Lake Storage konfigurace](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. V podokně Průzkumník dat vyberte **přístup** a klikněte na tlačítko **přidat** v podokně přístup.

   ![Konfigurace přístupu k Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Do textového pole na **vyberte uživatele nebo skupinu** podokně, zadejte název instančního objektu. Mějte na paměti, že název instančního objektu je také název odpovídající úlohy Stream Analytics. Během zadávání hlavní název se zobrazí pod textovým polem. Vyberte hlavní název požadované služby a klikněte na tlačítko **vyberte**.

   ![Vyberte hlavní název služby](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. V **oprávnění** podokně zaškrtněte **zápisu** a **Execute** oprávnění a přiřadit ji ke **tuto složku a všechny podřízené objekty**. Pak klikněte na tlačítko **Ok**.

   ![Vyberte zápisu a spouštěcích oprávnění](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Instanční objekt služby je uvedený v části **přiřazena oprávnění** na **přístup** podokně, jak je znázorněno níže. Teď můžete přejít zpět a spuštění vaší úlohy Stream Analytics.

   ![Stream Analytics přístup k seznamu v portálu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Další informace o oprávnění systému souborů Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics tools for Visual Studio

1. V JobConfig.json, nastavte **použijte systém přiřadil Identity** k **True**.

   ![Konfigurace úlohy Stream Analytics spravovaných identit](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. V okně výstupu vlastnosti výstupní jímky ADLS Gen1, klikněte na rozevírací seznam a vyberte režim ověřování **spravovaná identita (preview)**.

   ![ADLS výstup spravovaných identit](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vyplňte zbývající vlastnosti nástroje a klikněte na tlačítko **Uložit**.

4. Klikněte na tlačítko **odeslat do Azure** v editoru dotazů.

   Když odešlete úlohu nástroje udělat dvě věci:

   * Automaticky vytvoří službu objektu zabezpečení pro identifikaci úlohy Stream Analytics v Azure Active Directory. Azure bude spravovat životní cyklus nově vytvořený identity. Při odstranění úlohy Stream Analytics přidružený identity (to znamená, instanční objekt) Azure automaticky odstraní.

   * Automaticky nastavit **zápisu** a **Execute** oprávnění pro ADLS Gen1 předponová cesta použité v této úloze a přiřaďte ho ke tato složka a všechny podřízené objekty.

5. Pomocí následujících vlastností pomocí šablon Resource Manageru můžete vygenerovat [Stream Analytics CI. Balíček CD Nuget](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) verze 1.5.0 nebo vyšší na sestavovacím počítači (mimo sadu Visual Studio). Pomocí Resource Manageru šablony nasazení kroky v další části, kde získat službu objektu zabezpečení a udělte oprávnění pro instanční objekt pomocí Powershellu.

## <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

1. Můžete vytvořit *Microsoft.StreamAnalytics/streamingjobs* prostředků s využitím spravované identity zahrnutím následující vlastnosti v oddíle prostředků šablony Resource Manageru:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost říká Azure Resource Manageru můžete vytvořit a spravovat identitu pro vaši úlohu Azure Stream Analytics.

   **Ukázkové úlohy**

    ```json
    {
      "Name": "AsaJobWithIdentity",
      "Type": "Microsoft.StreamAnalytics/streamingjobs",
      "Location": "West US",
      "Identity": {
        "Type": "SystemAssigned",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
        "outputs": [
          {
            "name": "string",
            "properties":{
              "datasource": {
                "type": "Microsoft.DataLake/Accounts",
                "properties": {
                  "accountName": "myDataLakeAccountName",
                  "filePathPrefix": "cluster1/logs/{date}/{time}",
                  "dateFormat": "YYYY/MM/DD",
                  "timeFormat": "HH",
                  "authenticationMode": "Msi"
                }
              }
   ```
  
   **Ukázková odpověď úlohy**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
      }
   ```

   Poznamenejte si ID objektu zabezpečení z odpovědi úlohy udělit přístup k požadované prostředků ADLS.

   **ID Tenanta** je ID tenanta Azure Active Directory, kde se vytvoří instanční objekt služby. Instanční objekt se vytvoří v Azure, která je důvěryhodná pro předplatné.

   **Typ** označuje typ spravovanou identitu, jak je vysvětleno v typech spravovaných identit. Je podporován pouze typ systém přiřadil.

2. Poskytnout přístup k objektu zabezpečení pomocí prostředí PowerShell. Udělit přístup ke službě instančního objektu pomocí Powershellu, spusťte následující příkaz:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** je ID objektu instančního objektu a je uvedené na portálu obrazovka po vytvoření instančního objektu. Pokud jste vytvořili úlohu pomocí nasazení šablony Resource Manageru, ID objektu je uvedeno ve vlastnosti identita třídy odpověď úlohy.

   **Příklad:**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Další informace o výše uvedený příkaz Powershellu, přečtěte si [Set-AzDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentaci.

## <a name="limitations"></a>Omezení
Tato funkce nepodporuje následující:

1.  **Víceklientský přístup**: Instanční objekt služby, které jsou vytvořené pro danou úlohu Stream Analytics se bude nacházet v tenantovi Azure Active Directory, na kterém byla vytvořena úloha a nelze použít u prostředku, který se nachází v jiném tenantovi Azure Active Directory. Proto můžete použít pouze instalační služby MSI na 1. generace ADLS prostředky, které jsou ve stejném tenantovi Azure Active Directory jako vaší úlohy Azure Stream Analytics. 

2.  **[Identita uživatele přiřazeny](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**: není podporována. to znamená, že uživatel není možné je zadat své vlastní instanční objekt služby pro svoje úlohy Stream Analytics. Instanční objekt je generován Azure Stream Analytics. 


## <a name="next-steps"></a>Další postup

* [Vytvořit výstupní Data lake Store pomocí stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testování dotazů Stream Analytics místně pomocí sady Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Živá data test místně pomocí nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-live-data-local-testing.md) 
