```mermaid
flowchart TD
    Start([Start export1003EvProcess()])
    InsertData[Insert Template Data]
    SelectGroupFile[Select Group File]
    CheckGroup[GroupFile not empty?]
    LoopGroupFile{For each GroupFile}
    CreateExcel[Create Excel from Template]
    CloneSheet[Clone "DATA" sheet to new sheet]
    SelectGroupData[Select Group Data]
    CheckGroupData[GroupData not empty?]
    LoopGroupData{For each GroupData}
    WriteHeader1[Write Header: Grade]
    WriteHeader2[Write Header: Package2]
    WriteTab1[Write Tab Detail 1]
    WriteTab2[Write Tab Detail 2]
    WriteTab3[Write Tab Detail 3]
    WriteTab4[Write Tab Detail 4]
    WritePackage3[Write Header: Package3]
    WriteDetail[Write Data Detail]
    AddToZip[Add Excel to Zip list]
    GenerateZip[Generate Zip File]
    ReturnFile[Return Zip File to Client]
    DeleteTemp[Delete Temp Files]
    End([End Process])

    Start --> InsertData --> SelectGroupFile --> CheckGroup
    CheckGroup -- Yes --> LoopGroupFile
    LoopGroupFile --> CreateExcel --> CloneSheet --> SelectGroupData --> CheckGroupData
    CheckGroupData -- Yes --> LoopGroupData
    LoopGroupData --> WriteHeader1 --> WriteHeader2 --> WriteTab1 --> WriteTab2 --> WriteTab3 --> WriteTab4 --> WritePackage3 --> WriteDetail --> AddToZip
    AddToZip --> LoopGroupFile
    CheckGroupData -- No --> End
    CheckGroup -- No --> End
    LoopGroupFile --> GenerateZip --> ReturnFile --> DeleteTemp --> End
```
