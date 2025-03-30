 
 
## 📌 Sequence Diagram: export1003Process

```mermaid
sequenceDiagram
    participant Client
    participant Controller
    participant ExportService
    participant DB
    participant FileSystem
    participant ExcelHelper
    participant ZipHelper
    participant HttpResponse

    %% STEP 1: Logging เริ่มต้น
    Note over ExportService: STEP 1 - Start export1003Process()
    Client->>Controller: Request export1003Process()
    Controller->>ExportService: export1003Process(...)

    %% STEP 2: Define SQL Path และ Temp Folder
    Note over ExportService: STEP 2 - Define SQL paths, folder
    ExportService->>ExportService: Define SQL paths, temp folder

    %% STEP 3: Init Variables
    Note over ExportService: STEP 3 - Init variables, list, map
    ExportService->>ExportService: Init lists, maps, files

    %% STEP 4: Insert Temp Data
    Note over DB,ExportService: STEP 4 - Insert Temp Data
    ExportService->>DB: insertTemplateData(...)
    DB-->>ExportService: OK

    %% STEP 5: ดึง Group File
    Note over DB,ExportService: STEP 5 - Select Group File
    ExportService->>DB: selectListGroupFile(...)
    DB-->>ExportService: List<GroupFile>

    alt GroupFile not empty
        loop each GroupFile
            %% STEP 6.1: สร้าง Excel จาก Template
            Note over FileSystem,ExcelHelper: STEP 6.1 - Create Excel file
            ExportService->>FileSystem: createFileWithAutoGenerateFile()
            ExportService->>FileSystem: read Excel template
            ExportService->>ExcelHelper: clone workbook from template

            %% STEP 6.2: ดึง Group Data
            Note over DB,ExportService: STEP 6.2 - Select Group Data
            ExportService->>DB: selectListGroupData(carGroup, carAge)
            DB-->>ExportService: List<GroupData>

            alt GroupData not empty
                %% STEP 6.3: สร้าง Sheet ตาม Group Data
                Note over ExcelHelper: STEP 6.3 - Clone Sheet per GroupData
                loop each GroupData
                    ExcelHelper->>ExcelHelper: cloneSheet("DATA", sheetName)
                end
                ExcelHelper->>ExcelHelper: removeSheet("DATA")
                ExcelHelper->>FileSystem: write workbook

                %% STEP 7: เขียน Header & Detail
                Note over ExcelHelper,DB: STEP 7 - Write headers & detail
                loop each GroupData
                    ExportService->>ExcelHelper: set SQL header config (grade)
                    ExcelHelper->>DB: query header data
                    DB-->>ExcelHelper: header data
                    ExcelHelper->>FileSystem: write to sheet

                    ExportService->>ExcelHelper: set SQL header config (package2)
                    ExcelHelper->>DB: query header
                    ExcelHelper->>FileSystem: write

                    ExportService->>ExcelHelper: set SQL header config (tabDetail1-4)
                    ExcelHelper->>DB: query headers
                    ExcelHelper->>FileSystem: write each tab

                    ExportService->>ExcelHelper: set SQL header config (package3)
                    ExcelHelper->>DB: query
                    ExcelHelper->>FileSystem: write

                    ExportService->>ExcelHelper: set SQL detail config
                    ExcelHelper->>DB: query detail rows
                    ExcelHelper->>FileSystem: write detail data
                end

                %% STEP 8: เก็บไฟล์ลง list
                Note over ExportService: STEP 8 - Add file to listFile
                ExportService->>ExportService: add Excel to listFile + listFileName

            else GroupData is empty
                ExportService-->>Controller: throwException("GroupData is null")
            end
        end
    else No Group File
        ExportService-->>Controller: throwException("No group file found")
    end

    %% STEP 9: Zip ไฟล์ทั้งหมด
    Note over ZipHelper: STEP 9 - Generate zip file
    ExportService->>ZipHelper: generateZipFile(listFile, listFileName)
    ZipHelper->>FileSystem: create zip
    FileSystem-->>ZipHelper: zip file
    ZipHelper-->>ExportService: return zip file

    %% STEP 10: ส่งไฟล์กลับ Client
    Note over HttpResponse: STEP 10 - Send zip to client
    alt zipFile not empty
        ExportService->>HttpResponse: writeFileToClient(...)
    else
        ExportService-->>Controller: throwException("Zip file is empty")
    end

    %% STEP 11: ลบไฟล์ชั่วคราว
    Note over FileSystem: STEP 11 - Delete temp files
    loop each file in listFileForClear
        ExportService->>FileSystem: deleteFile(file)
    end

    %% STEP 12: จบ Process
    Note over Controller: STEP 12 - Done
    ExportService-->>Controller: Done
    Controller-->>Client: Return .zip file

 ```
 
