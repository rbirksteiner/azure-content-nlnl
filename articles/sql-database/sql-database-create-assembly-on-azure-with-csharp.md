<properties 
	pageTitle="Azure SQL Database 上包含 CSharp 的 CREATE ASSEMBLY"
	description="先將 DLL 檔案編碼到包含長十六進位數字的字串之後，提供 C# 原始程式碼，以便將 CREATE ASSEMBLY 發出到 Azure SQL Database。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="genemi"/>


# Azure SQL Database 上包含 CSharp 的 CREATE ASSEMBLY


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


本主題提供您可以將 [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) 陳述式發出到 Azure SQL Database 的 C# 程式碼範例。對於 SQL Database，FROM 子句在裝載資料庫的本機電腦上，無法接受簡單的路徑格式。替代方式是，先將組件 DLL 的二進位位元編碼到包含十六進位數字的長字串中。然後提供字串，當做 FROM 子句的值。


### 必要條件


為了解本主題，您必須有已部分知道下列：


- [CLR 資料表值函數](http://msdn.microsoft.com/library/ms131103.aspx)<br/>說明 CREATE ASSEMBLY Transact-SQL 陳述式如何搭配內部部署 Microsoft SQL Server 的其他陳述式使用。


## 答：整體的技巧


1. DROP FUNCTION 和 DROP ASSEMBLY (如果需要清除上一個執行)。
2. 請記住您從自己的程式碼編譯的 Microsoft.NET Framework 組件 DLL 檔案的位置。您要在下一個步驟中提供該位置。 
3. 執行本主題中所提供之 C# 原始程式碼使用的 EXE。告訴 EXE 您的 DLL 檔案所在位置。
 - 將您的二進位 DLL 編碼到包含十六進位數字的長字串中。
 - 使用 FROM 子句中提供的十六進位字串，發出 CREATE ASSEMBLY 陳述式。
4. [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) 用於參考組件中的方法。
5. T-SQL SELECT 陳述式用於呼叫並測試函數。


上述清單並未提到...<br/> **execute sp_configure 'clr enabled', 1;**<br/> ...因為即使 Microsoft SQL Server 需要用到，但 Azure SQL Database 並不需要。


如果需要重新執行，卸除函數與組件的 T-SQL 程式碼為以下的：


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B.用於參考之 T-SQL 函數的簡單組件 DLL


本節中的簡單式 C# 程式碼範例可以編譯到組件 DLL 檔案中。


此程式碼範例所包含的 **CompareCaseSensitiveNet** 方法，會在稍後的 T-SQL CREATE FUNCTION 陳述式中參考。請注意，方法是使用名稱為 **SqlFunction** 的 .NET 屬性裝飾。使用此屬性裝飾的方法可以透過 T-SQL，當做函數呼叫。


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C.C&#x23; 發出 CREATE ASSEMBLY 之 EXE 的程式碼範例


當您執行從此 C# 範例建置的 EXE 時，會發生以下順序：


1. EXE 的命令列執行會呼叫 **Main** 方法。
2. Main 會呼叫 **ObtainHexStringOfAssembly** 方法。
 - 此方法會輸出以十六進位數字儲存組件的 SqlString。
3. Main 會呼叫 **SubmitCreateAssemblyToAzureSqlDb** 方法。
 - 主要輸入為 SqlString。
 - 輸出為傳送至 Azure SQL Database 的 CREATE ASSEMBLY 呼叫。


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 編譯參考和版本


當我們編譯並測試 EXE 工具的範例程式碼時，我們使用以下的：


- Visual Studio 2013 Update 4
 - 我們的專案範本類型為簡單的主控台應用程式。
- .NET Framework 4.5


我們的 Visual Studio 專案參考用於編譯的以下組件：


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 執行 EXE 的命令列


以下程式碼區塊會顯示您為從主控台執行 EXE 而輸入之命令列的範例。命令列中的參數會以人為方式，在這裡包裝，以獲得更好的顯示效果。


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


為簡化說明，此範例會以命令列參數傳遞密碼。較好的設計是讓 C# 程式碼從 CONFIG 檔案取得密碼。


## D.執行 CREATE FUNCTION 陳述式


在組件儲存到 Azure SQL Database 伺服器之後，您必須執行參考組件中的方法的 Transact-SQL CREATE FUNCTION 陳述式。


Transact-SQL 程式碼的以下區塊包含數個不重要的 SELECT 陳述式，以證明資料庫系統擁有您組件和函數的記錄。最後，有一個呼叫函數的 SELECT。


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


上述的 Transact-SQL 程式碼區塊結尾是呼叫新函數的 SELECT 陳述式。您可以將 SELECT 陳述式放入預存程序中。


<!-- EndOfFile -->

<!---HONumber=July15_HO2-->