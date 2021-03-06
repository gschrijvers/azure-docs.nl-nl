---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: e30651cb0ed7d74082163a92acbc428c21018255
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176113"
---
## <a name="c-program-example"></a>Voorbeeld van C#-programma

In de volgende secties van dit artikel wordt een C#-programma gepresenteerd dat gebruikmaakt van ADO.NET voor het verzenden van Transact-SQL-instructies (T-SQL) naar de SQL Database. Met het C#-programma worden de volgende acties aangetoond:

- [Verbinding maken met SQL Database met behulp van ADO.NET](#cs_1_connect)
- [Methoden voor het retourneren van T-SQL-instructies](#cs_2_return)
    - Tabellen maken
    - Tabellen vullen met gegevens
    - Gegevens bijwerken, verwijderen en selecteren
- [T-SQL indienen bij de database](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>ERD (diagram Relatie tussen eenheden)

Voor de `CREATE TABLE`-instructies is het trefwoord **VERWIJZINGEN** nodig voor het maken van een FK-relatie (*refererende sleutel*) tussen twee tabellen. Als u *tempdb* gebruikt, geeft u commentaar bij het trefwoord `--REFERENCES` met behulp van twee streepjes aan het begin.

Op de ERD wordt de relatie tussen de twee tabellen weergegeven. De waarden in de *onderliggende* kolom **tabEmployee. DepartmentCode** zijn beperkt tot waarden uit de *bovenliggende* kolom **tabDepartment. DepartmentCode** .

![ERD met refererende sleutel](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> U kunt de T-SQL bewerken om een `#` toe te voegen vóór de tabelnamen. Hierdoor worden ze gemaakt als tijdelijke tabellen in *tempdb*. Dit is nuttig voor demonstratiedoeleinden, wanneer er geen testdatabase beschikbaar is. Er worden geen verwijzingen naar refererende sleutels afgedwongen wanneer deze worden gebruikt. Tijdelijke tabellen worden automatisch verwijderd wanneer de verbinding wordt verbroken zodra het programma is uitgevoerd.

### <a name="to-compile-and-run"></a>Compileren en uitvoeren

Het C#-programma is logischerwijs één .cs-bestand en fysiek onderverdeeld in verschillende codeblokken, waardoor elk blok gemakkelijker te begrijpen is. Ga als volgt te werk om dit programma te compileren en uit te voeren:

1. Maak een C#-project in Visual Studio. Het projecttype moet *Console* zijn. U vindt dit onder **Sjablonen** > **Visual C#** > **Windows Desktop** > **Console App (.NET Framework)**.

1. In het bestand *Program.cs* vervangt u de beginregels van de code aan de hand van de volgende stappen:

    1. Kopieer en plak de volgende codeblokken in de volgorde waarin ze worden gepresenteerd (zie [Verbinding maken met de database](#cs_1_connect), [T-SQL genereren](#cs_2_return) en [Indienen bij database](#cs_3_submit)).

    1. Wijzig de volgende waarden in de `Main`-methode:

        - *cb.DataSource*
        - *cb.UserID*
        - *cb.Password*
        - *cb.InitialCatalog*

1. Controleer of wordt verwezen naar de assembly *System.Data.dll*. Vouw in het deelvenster **Solution Explorer** het knooppunt **Verwijzingen** uit om dit te controleren.

1. Selecteer de knop **Start** om het programma te bouwen en uit te voeren in Visual Studio. De rapportuitvoer wordt in een programmavenster weergegeven, hoewel GUID-waarden tussen testuitvoeringen zullen variëren.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>Verbinding maken met SQL Database met behulp van ADO.NET

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>Methoden voor het retourneren van T-SQL-instructies

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>T-SQL indienen bij de database

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```