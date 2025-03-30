```mermaid 
flowchart TD
    A1[STEP-1: Logging เริ่มต้น] --> A2[STEP-2:กำหนด SQL Path และ Temp Folder]
    A2 --> A3[STEP-3:Init Variables]
    A3 --> A4[STEP-4:Insert Temp Data]
    A4 --> A5[STEP-5:Select Group File]
    A5 --> A6{STEP-6:มี Group File?}

    A6 -- No --> AE1[Throw Exception:No Group File] --> AE[END]
    A6 -- Yes --> B1[STEP-6.1:Create Excel จาก Template]
    B1 --> B2[STEP-6.2:Select Group Data]
    B2 --> B3{มี Group Data?}

    B3 -- No --> AE2[Throw Exception: GroupData is null] --> AE
    B3 -- Yes --> B4[STEP-6.3:Clone Sheet จาก Template]
    B4 --> C1[STEP-7:เขียน Header + Detail_7ส่วน]
    C1 --> D1[STEP-8:เก็บไฟล์ลง List]

    D1 --> A6

    D1 --> E1[STEP-9:ZIP ไฟล์ทั้งหมด]
    E1 --> E2{ZIP file OK?}
    E2 -- No --> AE3[Throw Exception: ZIP is empty] --> AE
    E2 -- Yes --> E3[STEP-10:ส่งไฟล์กลับ Client]
    E3 --> E4[STEP-11:ลบไฟล์ชั่วคราว]
    E4 --> AE[STEP-12:END]
```
 
