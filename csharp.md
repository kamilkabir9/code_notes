# C#

## Calling SQL Stored procedure.
- Calling SQL Stored procedure with input parameters
Call a SQL stored procedure with input 
```csharp
   try
   {
    using (SqlConnection con = new SqlConnection(connectionString))
    {
     using (SqlCommand cmd = new SqlCommand("sp_get_req", con))
      {
       cmd.CommandType = CommandType.StoredProcedure;
       cmd.Parameters.AddWithValue("param1", "REQ-123");
       cmd.Parameters.AddWithValue("param2", "param2value");
       con.Open();
       cmd.ExecuteNonQuery();
      }
    }
   }
   catch (Exception e)
   {
    throw e;
   } 
```

- Calling SQL Stored procedure with input parameters
Call SQL stored procedure with input and reading returned data.
```csharp
    try
    {    
        using (SqlConnection con = new SqlConnection(connectionString))
        {
            using (SqlCommand cmd = new SqlCommand(SP_get_Requests, con))
            {
                cmd.CommandType = CommandType.StoredProcedure;
                cmd.Parameters.AddWithValue("param1", "param1Value");            
                con.Open();
                using (SqlDataReader sdr = cmd.ExecuteReader())
                {
                    while (sdr.Read())
                    {
                        string reqNumber=sdr["reqNumber"].toString();
                    }
                }
            }
        }
    }
    catch (Exception e)
    {
        throw e;
    }           
```

- Calling SQL Stored procedure with input parameters and store as DataTable.
Call SQL stored procedure with input and store returned data as DataTable.
```csharp
    DataTable result = new DataTable();
            try
            {
                using (var con =
                    new SqlConnection(ConfigurationManager.ConnectionStrings["DBConnection"].ConnectionString))
                {
                    using (var cmd = new SqlCommand(spName.Trim(), con))
                    {
                        cmd.Parameters.AddWithValue("pastDate", endDate);
                        cmd.Parameters.AddWithValue("presentDate", startDate);
                        using (var da = new SqlDataAdapter(cmd))
                        {
                            cmd.CommandType = CommandType.StoredProcedure;
                            da.Fill(result);
                        }
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
            return result;     
```

- SQL execute as string
Execute sql as string inside procedure.
```xml
<add key="Projects" value="P1;P2;P3;P3;"/>
```
```csharp
List<string> neededProjects = ConfigurationManager.AppSettings["Projects"].ToString().Split(';').TakeWhile(ele => ele != "").ToList();
            neededProjects=neededProjects.ConvertAll(x => { return string.Format("'{0}'",x); });
            string neededProjectsCleaned = string.Join(",", neededProjects);
SqlCommand cmd = new SqlCommand(SP_Project_Report, conConnection);
cmd.Parameters.AddWithValue("ProjectCode_param", neededProjectsCleaned);
                DataSet ds = new DataSet();
                cmd.CommandType = CommandType.StoredProcedure;
                da.SelectCommand = cmd;
                da.Fill(ds);
```
```sql
CREATE PROCEDURE dbo.SP_BMRByProject_Report
    @ProjectCode_param  VARCHAR(150)= 0,
AS
    declare @final_query VARCHAR(MAX);
    SET @final_query='';
    
    DECLARE @qr1 VARCHAR(MAX);
    set @qr1='SELECT ReqNo, ReqDt, RequesterName, ReqEmpName AS RequestFor, 
                ReqEmpNo AS RequestForEmpNo, ServiceNames, 
                V_BMRServiceAdmin.Status AS statusC, Category AS PendingAt, 
                ProjectCode AS projectCode FROM table1
                WHERE 1=1 ';

    if LEN(LTRIM(RTRIM(@ProjectCode_param)))>0
    BEGIN
     SET @qr1 = @qr1+' '+' AND ProjectCode IN (' + @ProjectCode_param + ') '    
     SET @final_query= @final_query +' '+ @qr1
    END

    PRINT 'exec following query'
    PRINT @final_query

    EXEC(@final_query)
GO
```


## Logging
Application logs can be written using Nlog.
1. Install Nlog and Nlog.config by running `Install-Package NLog.Config` in Tools -> Nuget Package Manager -> Package Manager Console.
2. Set Nlog.config file as
```xml
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.nlog-project.org/schemas/NLog.xsd NLog.xsd"
      autoReload="true"
      throwExceptions="true"
      throwConfigExceptions="true"
      internalLogLevel="Fatal" internalLogFile="C:\nlog-internal.log"> 

        <!-- optional, add some variables
  https://github.com/nlog/NLog/wiki/Configuration-file#variables
  -->

  <!--
  See https://github.com/nlog/nlog/wiki/Configuration-file
  for information on customizing logging rules and outputs.
   -->
  <targets>

     <!--
    add your targets here
    See https://github.com/nlog/NLog/wiki/Targets for possible targets.
    See https://github.com/nlog/NLog/wiki/Layout-Renderers for the possible layout renderers.
    -->
   
    <target name="logfile" xsi:type="File" fileName="${basedir}/MyProgramm_log-${date:format=dd-MMM-yyyy}.txt"
            layout="${date:format=dd-MMM-yyyy HH\:mm}|${uppercase:${level}}|Machine:${machinename}|Line:${callsite-linenumber:skipFrames=0}|${message}|${exception:format=ToString:innerFormat=ToString:maxInnerExceptionLevel=0:innerExceptionSeparator= :separator=String:exceptionDataSeparator=;}${newline}"/>
  </targets>

  <rules>   
    <!--
    Write all events with minimal level of Debug (So Debug, Info, Warn, Error and Fatal, but not Trace)  to "f"
    <logger name="*" minlevel="Debug" writeTo="f" />
    -->
    <logger name="*" minlevel="Debug" writeTo="logfile" />
  </rules>
</nlog>

```
3. Write logs
```csharp
 public static NLog.Logger nlog = NLog.LogManager.GetCurrentClassLogger();
 nlog.Info($"----------------------------Program Started ---------------------");
```

## Reading Configuration
- Add reference ```System.Configuration```
- Reading Appsettings
    ```xml
    <appSettings>
    <add key="servicenow_URL" value="https://www.servicenow.com" />
    </appSettings>
    ```
    ```csharp
    string snowURL = ConfigurationManager.AppSettings["servicenow_URL"].ToString().Trim();
    ```

- Reading ConnectionStrings
    ```xml
    <connectionStrings>
        <add name="DevDB" providerName="System.Data.SqlClient" connectionString="Data Source=ED003VM0734\DEV2012;Persist Security Info=False;Trusted_Connection=True;Connect Timeout=30;Pooling=true;Database=DevDB" />
    </connectionStrings>
    ```

    ```csharp
    public static string connectionString =ConfigurationManager.ConnectionStrings["DevDB"].ConnectionString;
    ```
- Reading delimited values
    ```xml
    <add key="EmailCC" value="usr1@gmail.com;usr64@gmail.com" />
    ```

    ```csharp
     List<string> emailToCC = ConfigurationManager.AppSettings["EmailCC"].ToString().Split(';').Where(x => x != "").ToList();
    ```
## Decode and Encode HTML strings
- decode and encode HTML strings
    ```csharp
        string a = WebUtility.HtmlEncode("<html><head><title>T</title></head></html>");
        string b = WebUtility.HtmlDecode(a);
    ```
## Read file contents
- Read file contents into string variable
    ```csharp
     string templateHTML = File.ReadAllText(Path.Combine(Environment.CurrentDirectory, "./EmailTemplate/pendingWithYou.html"), System.Text.Encoding.UTF8);
    ```    
## Get All file names in Dir
- Get All file names in Dir
    ```csharp
    string[] fileArray = Directory.GetFiles(@"C:\Users\kamil\Email_Bounced\", "*.msg");
    ```
- Get All files in Dir and in subDir
    ```csharp
        public static List<string> getAllChildrenInfo(string rootDir) {
            List<string> result = new List<string>();
            try
            {
                string[] fileArray = Directory.GetFiles(rootDir,"*.*",SearchOption.AllDirectories);
            }
            catch (Exception ex)
            {
                throw ex;
            }
            return result;
        }
    ```
## Create File
- Create File
```csharp
string dir = @"C:\Users\kkabir\\Email_Bounced\";
                string xlsFileName = $"Output_{DateTime.Now.ToString("yy-MM-dd_HHmmss")}.xlsx";
                xlsFileName = Path.Combine(dir, xlsFileName);
                FileStream fs = File.Create(xlsFileName);
                fs.Close();
```
## String Templates using [stringtemplate](https://www.stringtemplate.org/) 
- String Templates using [stringtemplate](https://www.stringtemplate.org/)

    Install StringTemplate4 by going to *tools > Nuget Package manager > Package manager console*
    ```powershell
    Install-Package StringTemplate4 -Version 4.0.8
    ```
    Use StringTemplate.
    ```csharp
    using Antlr4.StringTemplate;
    Template hello = new Template("Hello, $name$", '$', '$');
		hello.Add("name", "World");
		string render=hello.Render();
		Console.Out.WriteLine(render);
    ```
## TimeStamped File name
- TimeStamped File name
 ```csharp
 string filenameTimeStamped = $@"C:\Users\usr1\Desktop\ExpiryReport\GeneratedReports\Report_{DateTime.Now.ToString("yyyyMMdd-HHmmss_ddMMMyyy")}.xlsx";
 ```   
## Linq
- Linq to Object using GroupBy and ToDictionary
 ```csharp
/*   OwnerGrp       AssignmentGroup    AssignedTo  rows */
var _ownergroup = "OwnerGroup";
var _assgmnt_Grp = "AssignmentGroup";
var _assgmnt_To = "AssignedTo";
 Dictionary<string,Dictionary<string,Dictionary<string,List<DataRow>>>> resultDictionary = dataTable.AsEnumerable().GroupBy(r => r[_ownergroup]).OrderByDescending(k=>k.Key.ToString()).ToDictionary(k => k.Key.ToString(),
                                    v => v.GroupBy(r => r[_assgmnt_Grp]).OrderByDescending(k=>k.Key.ToString())
                                          .ToDictionary(k2 => k2.Key.ToString(),
                                                        v2 => v2.GroupBy(r => r[_assgmnt_To]).OrderBy(k=>k.Key.ToString())
                                                                .ToDictionary(k3 => k3.Key.ToString(), v3 => v3.OrderBy(r => r["IncidentNumber"].ToString()).ToList())));
                resultDictionary = resultDictionary.OrderBy(x => x.Key).ToDictionary(x => x.Key, x => x.Value);
 ```
## Create Excel reports
- Create Excel reports using [EPPlus](https://github.com/JanKallman/EPPlus). Make sure to download EPPlus from nuget.
```csharp
string filenameTimeStamped = $@"C:\Users\usr1\Desktop\ExpiryReport\GeneratedReports\Report_{DateTime.Now.ToString("yyyyMMdd-HHmmss_ddMMMyyy")}.xlsx";
                //write date to execl file
                //Open the workbook (or create it if it doesn't exist)
                var fi = new FileInfo(filenameTimeStamped);
                using (var p = new ExcelPackage(fi))
                {
                    if (p.Workbook.Worksheets.Count < 1)
                    {
                        p.Workbook.Worksheets.Add("Report");
                    }
                    var ws = p.Workbook.Worksheets[1];
                    //write dataTable to excel.
                    ws.Cells[2, 1].LoadFromDataTable(dt, true, TableStyles.Light14);
                    ws.Column(1).AutoFit();
                    ws.Column(3).AutoFit();
                    ws.Column(4).AutoFit();
                    ws.Column(5).AutoFit();
                    ws.Column(6).AutoFit();
                    ws.Column(7).AutoFit();
                    ws.Column(8).AutoFit();
                    ws.Column(9).AutoFit();
                    ws.Column(10).AutoFit();
                    ws.Column(11).AutoFit();
                    //save with password
                    p.Save("root");
                    return filenameTimeStamped;
                }
```
```csharp
 var fi = new FileInfo(filenameTimeStamped);

                    using (var p = new ExcelPackage(fi))
                    {
                        if (p.Workbook.Worksheets.Count < 1)
                        {
                            p.Workbook.Worksheets.Add("Report");
                        }

                        var ws = p.Workbook.Worksheets[1];
                        int index = 1;
                        Image img = Image.FromFile($@"{ConfigurationManager.AppSettings["logo"].ToString()}");
                        ExcelPicture pic = ws.Drawings.AddPicture("TechnipFMC_logo", img);
                        pic.SetPosition(0, 5, 0, 5);
                        index+=4;
                        ws.Cells[index,1].Value = $"Telecom Report : {prj.projectCode} - {prj.projectName} : {reportingDate.ToString("yyyy - MMMM")}";
                        ws.Cells[index, 1, index, 10].Merge = true;
                        ws.Cells[index, 1, index, 10].Style.Font.Size = 18;
                        ws.Cells[index, 1, index, 10].Style.Font.Italic = true;
                        ws.Cells[index, 1, index, 10].Style.Font.Bold = true;
                        ws.Cells[index, 1, index, 10].Style.Font.UnderLine = true;
                        ws.Cells[index, 1, index, 10].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                        ws.Cells[index, 1, index, 10].Style.Font.Color.SetColor(0, 0, 112, 192);

                        index++;
                        ws.Cells[index,1].Value = $"Access Code:{callDetailKeyValuePair.Key}";
                        ws.Cells[index, 1, index, 10].Merge = true;
                        ws.Cells[index, 1, index, 10].Style.Font.Size = 18;
                        ws.Cells[index, 1, index, 10].Style.HorizontalAlignment=ExcelHorizontalAlignment.Center;
                        ws.Cells[index, 1, index, 10].Style.Font.Color.SetColor(0,0,112,192);

                        //write dataTable to excel.
                        //header
                        index += 2;
                        int tableStartRow = index;
                        ws.Cells[index, 1].Value = "Call Date";
                        ws.Cells[index, 2].Value = "Call Time";
                        ws.Cells[index, 3].Value = "Call From";
                        ws.Cells[index, 4].Value = "Call To";
                        ws.Cells[index, 5].Value = "Call Duration";
                        ws.Cells[index, 6].Value = "Call Type";
                        ws.Cells[index, 7].Value = "Access Code";
                        ws.Row(index).Height=40;
                        ws.Cells[index, 1, index, 7].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                        ws.Cells[index, 1, index, 7].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                        index++;
                        foreach (var callDetail in callDetailKeyValuePair.Value.OrderBy(call=>call.callTime.Ticks))
                        {
                            //data
                            ws.Cells[index, 1].Value = callDetail.callTime.ToLocalTime().ToString("dd-MMM-yyyy");
                            ws.Cells[index, 2].Value = callDetail.callTime.ToLocalTime().ToString("hh:mm:ss tt");
                            ws.Cells[index, 3].Value = callDetail.callFrom;
                            ws.Cells[index, 4].Value = callDetail.callTo;
                            ws.Cells[index, 5].Value = TimeSpan.FromSeconds(callDetail.callDuration).ToString();
                            ws.Cells[index, 6].Value = callDetail.callType;
                            ws.Cells[index, 7].Value = callDetailKeyValuePair.Key;
                            ws.Cells[index, 7].Style.HorizontalAlignment = ExcelHorizontalAlignment.Right;
                            index++;
                        }
                        ExcelRange rg = ws.Cells[tableStartRow, 1, ws.Dimension.End.Row, 7];
                        //Ading a table to a Range
                        ExcelTable tab = ws.Tables.Add(rg, "Table1");
                        tab.TableStyle = TableStyles.Light9;
                        //Auto Fit 
                        ws.Column(1).AutoFit();
                        ws.Column(2).AutoFit();
                        ws.Column(3).AutoFit();
                        ws.Column(4).AutoFit();
                        ws.Column(5).AutoFit();
                        ws.Column(6).AutoFit();
                        ws.Column(7).AutoFit();

                        p.Save();
                    }
```
## Save DataTable as Excel File with proper formatting(date)
```csharp
    public static string ExportToExcel (ref DataTable dt,string reportName)
        {
           string fileName = $@"Report-MCRS_{reportName}_{DateTime.Now:yyyyMMdd_HHmmsss}.xlsx";
           string filePath = Path.Combine(ConfigurationManager.AppSettings["ExportFileLoc"].ToString(),fileName);
           //write date to execl file
           //Open the workbook (or create it if it doesn't exist)
           var fi = new FileInfo(filePath);
           using (var p = new ExcelPackage(fi))
           {
               if (p.Workbook.Worksheets.Count< 1)
               {
                p.Workbook.Worksheets.Add("Report");
               }
               var ws = p.Workbook.Worksheets[0];
               //write dataTable to excel.
               ExcelRangeBase range=ws.Cells[2, 1].LoadFromDataTable(dt, true, TableStyles.Light14);
               //dt.Columns[0].DataType
               for (int i = 0; i < dt.Columns.Count; i++)
               {
                   Type type = dt.Columns[i].DataType;
                   if (type == Type.GetType("System.DateTime"))
                   {
                       //ws col starts from 1 
                       ws.Column(i+1).Style.Numberformat.Format = "dd-mmm-yyyy";
                   }
               }
               range.AutoFitColumns();                
               p.Save();
               nlog.Info($"Added data {dt.Rows.Count} to Excel File {filePath}");
               return fileName;
           }
        }
```
## Download file from WebAPi/Send file to client
- In Controller.cs file add the following.
```csharp
[System.Web.Http.HttpGet]
        public HttpResponseMessage ExportData()
        {
            if (IsAdmin() == "1"){
                string fileName = new ExportData().CreateFile();
                var dataBytes = File.ReadAllBytes(fileName);
                var dataStream = new MemoryStream(dataBytes);

                HttpResponseMessage httpResponseMessage = Request.CreateResponse(HttpStatusCode.OK);
                httpResponseMessage.Content = new StreamContent(dataStream);
                httpResponseMessage.Content.Headers.ContentDisposition =
                    new System.Net.Http.Headers.ContentDispositionHeaderValue("attachment");
                httpResponseMessage.Content.Headers.ContentDisposition.FileName = Path.GetFileName(fileName);
                httpResponseMessage.Content.Headers.ContentType =
                    new MediaTypeHeaderValue(MimeMapping.GetMimeMapping(fileName));
                return httpResponseMessage;
            }
            else
            {
                return Request.CreateResponse(HttpStatusCode.InternalServerError, "UnAuthorized");
            }
        }
```
- In Client side call the api
```javascript
    $scope.ExportData = function () {
        console.log('Export data');
        window.open(appSettings.apiUrl + 'api/Profile/ExportData', '_blank', '');
    }
```
## Excel Table to C# DataTable
- Convert Excel table to C# datatable.
```csharp
public static DataTable ExcelPackageToDataTable(string FilePath)
        {

            FileInfo existingFile = new FileInfo(FilePath);
            ExcelPackage package = new ExcelPackage(existingFile);

            DataTable dt = new DataTable();
            ExcelWorksheet worksheet = package.Workbook.Worksheets[1];

            //check if the worksheet is completely empty
            if (worksheet.Dimension == null)
            {
                return dt;
            }

            //create a list to hold the column names
            List<string> columnNames = new List<string>();

            //needed to keep track of empty column headers
            int currentColumn = 1;

            //loop all columns in the sheet and add them to the datatable
            foreach (var cell in worksheet.Cells[1, 1, 1, worksheet.Tables[0].Address.Columns])
            {
                string columnName = cell.Text.Trim();

                //check if the previous header was empty and add it if it was
                if (cell.Start.Column != currentColumn)
                {
                    columnNames.Add("Header_" + currentColumn);
                    dt.Columns.Add("Header_" + currentColumn);
                    currentColumn++;
                }

                //add the column name to the list to count the duplicates
                columnNames.Add(columnName);

                //count the duplicate column names and make them unique to avoid the exception
                //A column named 'Name' already belongs to this DataTable
                int occurrences = columnNames.Count(x => x.Equals(columnName));
                if (occurrences > 1)
                {
                    columnName = columnName + "_" + occurrences;
                }

                //add the column to the datatable
                dt.Columns.Add(columnName);

                currentColumn++;
            }

            //start adding the contents of the excel file to the datatable
            for (int i = 2; i <= worksheet.Tables[0].Address.Rows; i++)
            {
                var row = worksheet.Cells[i, 1, i, worksheet.Tables[0].Address.Columns];
                DataRow newRow = dt.NewRow();

                //loop all cells in the row
                foreach (var cell in row)
                {
                    newRow[cell.Start.Column - 1] = cell.Text;
                }

                dt.Rows.Add(newRow);
            }

            return dt;
        }
```