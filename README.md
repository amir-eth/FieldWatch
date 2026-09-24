# FieldWatch: Digital Disease Surveillance & Outbreak Detection Platform
 
FieldWatch is an agricultural and veterinary disease surveillance system built for rural smallholder farmers and district veterinary/agricultural extension officers. It works both online and offline: reports are sent straight to the server when a connection is available, and saved on the device and synced later when it is not. This bridges field observations in remote regions with central epidemiology surveillance and rapid outbreak response.
 
---
 
## Core Features
 
### 1. Farmer Field Reporting (`FarmerScreen`)
- **Rapid Case Entry**: Report sick or dead crops and livestock with species selection (Cattle, Small Ruminants, Swine, Poultry, Maize, Cassava, Rice).
- **Differential Symptom Engine (`SymptomEngine`)**: Interactive visual checklist of observed symptoms that calculates matching confidence scores across high-consequence diseases.
- **Online and Offline Reporting (`Outbox`)**:
  - **Online**: reports are submitted to the server immediately.
  - **Offline**: reports are saved in a persistent local queue on the device, so nothing is lost in areas with no coverage.
  - **Automatic sync**: queued reports are sent to the server as soon as connectivity is restored, with no action needed from the farmer.
- **Geotagging & Proximity**: Captures GPS coordinates, district boundary data, and calculates distance to nearest veterinary health posts.
- **Multi-lingual Support (`i18n`)**: Localized for field accessibility in English, Kiswahili (Swahili), Hindi, and Spanish.
### 2. Surveillance & Outbreak Detection (`OfficerScreen`)
- **Spatiotemporal Outbreak Clustering (`OutbreakDetector`)**:
  - Implements the Haversine distance algorithm to group reports geographically within a radius ($R$, e.g., 20 km).
  - Evaluates sliding temporal windows ($T$, e.g., 7–14 days).
  - Triggers **Outbreak Warning** when incident thresholds ($\theta$) are reached for specific suspect pathogens.
- **Geospatial Surveillance Map**: Real-time map displaying reported cases, hot-spot clusters, quarantine buffer radiuses, and movement restriction zones. Reports made offline appear on the map once they sync.
- **Case Verification & Action Workflow**: Allows surveillance officers to triage reports, update status (Suspect $\rightarrow$ Verified $\rightarrow$ Quarantined $\rightarrow$ Resolved), and dispatch rapid veterinary field teams.
- **Alert Broadcast & Advisory**: Automated SMS/radio broadcast advisory generation for neighboring farms within the transmission buffer zone.
---
 
## How Sync Works
 
1. A farmer submits a report. If the device is online, it goes directly to the server.
2. If the device is offline, the report is stored in the local `Outbox` queue.
3. When the device reconnects, the queue is sent to the server's sync endpoints in the order the reports were created.
4. The server stores the reports, and the `OutbreakDetector` includes them in its clustering.
Because offline reports reach the server later, outbreak detection uses the time each case was **observed**, not the time it was received. This keeps the temporal windows accurate even when a report arrives days late.
 
---
 
## Architecture
 
FieldWatch follows Kotlin Multiplatform (KMP) architecture:
- **`shared/`**: Common business logic, data models, symptom engine, geospatial calculations, and outbreak detection algorithms compiled to JVM, Android, and Web targets.
- **`server/`**: Ktor / Kotlin backend server providing REST API and persistent store for disease reports, outbreak clusters, and sync endpoints.
- **`composeApp/`**: Compose Multiplatform user interface with adaptive layouts for Farmer and Officer roles.
