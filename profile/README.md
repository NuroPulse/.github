<div align="center">

# NuroPulse
![Flutter](https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white)
![React Router](https://img.shields.io/badge/React_Router-CA4245?style=for-the-badge&logo=reactrouter&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)


NuroPulse is a **patient-to-neurologist companion ecosystem for patients with Parkinson’s** that turns quick daily check-ins into clear insights shared with their neurologists through **medicine logging** and a **biomarker-based Parkinson's tremor detection**.

Built with O(∩_∩)O for JOINTS Hackathon 2026 ⚙️

</div>

## System Architecture

```mermaid
flowchart LR
    subgraph MOBILE["Mobile app (Flutter) - patient"]
        direction TB
        UI["Presentation<br/>go_router, flutter_bloc Cubit, fl_chart"]
        DOM["Domain<br/>entities, repository interfaces, use cases"]
        DATA["Data<br/>repository impl, DTOs, supabase_flutter"]
        HIVE[("hive_ce<br/>local results + outbox<br/>flutter_secure_storage: session")]
        subgraph DSPM["DSP module (isolate, on-device)"]
            direction TB
            SENS["sensors_plus<br/>accelerometer ~100 Hz"]
            PIPE["QC gate, resample 50 Hz, band-pass 1-20 Hz<br/>Welch PSD, features, rule-based decision"]
            SENS --> PIPE
        end
        UI --> DOM --> DATA
        DATA --> HIVE
        DOM --> SENS
        PIPE --> DOM
    end

    subgraph WEB["Web dashboard (React Router) - neurologist"]
        direction TB
        WUI["Routes / loaders<br/>patient list, trend charts, medication timeline"]
        WCL["supabase-js client<br/>email + password session"]
        WUI --> WCL
    end

    subgraph SUPA["Supabase backend"]
        direction TB
        AUTH["Auth<br/>anonymous sign-in (patient)<br/>email + password (doctor)<br/>JWT: role authenticated"]
        API["PostgREST + RPC<br/>connect_to_doctor, disconnect,<br/>create_measurement (idempotent on id),<br/>list_my_measurements,<br/>list_patient_measurements"]
        subgraph PG["Postgres + Row Level Security"]
            direction TB
            T1[("profiles<br/>id, role, display_name, connection_code")]
            T2[("patient_doctor_links<br/>patient_id, doctor_id, linked_at, revoked_at")]
            T3[("measurements<br/>status, features jsonb, algo_version,<br/>calibrated, last_dose_at, device jsonb")]
            T4[("medication_schedules / medication_logs<br/>contract pending")]
        end
        AUTH --> API
        API --> PG
    end

    DATA --> AUTH
    DATA --> API
    WCL --> AUTH
    WCL --> API
    API -.-> WCL

    classDef store fill:#e8f0fe,stroke:#2941a3,color:#141a2e
    classDef pending fill:#fff5c2,stroke:#735c00,color:#735c00
    class HIVE,T1,T2,T3 store
    class T4 pending
```

## Demo

Coming Soon yh

## Team

Built by **nama timnya apa**, a 4-person team for JOINTS Hackathon 2026 from the Faculty of Computer Science, Universitas Indonesia 💾

| | Name | Institution | Role |
| :-: | --- | --- | --- |
| <img src="https://github.com/RisyadAthaya.png" width="64" alt="Name"> | **Risyad Athaya Muhammad** | Information Systems '25 @ Universitas Indonesia | **Team Leader** & Backend Engineer |
| <img src="https://github.com/zikabyte.png" width="64" alt="Zika"> | **Mohammad Zidane Kurnianto** | Information Systems '25 @ Universitas Indonesia | Mobile Engineer |
| <img src="https://github.com/raynakyla.png" width="64" alt="Name"> | **Rayna Kayla Rayvanka** | Computer Science '25 @ Universitas Indonesia | Business Lead |
| <img src="https://github.com/tanniajju.png" width="64" alt="Name"> | **Tania Ju** | Information Systems '25 @ Universitas Indonesia | Frontend Engineer |
