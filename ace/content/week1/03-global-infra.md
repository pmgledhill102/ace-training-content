# Global Infrastructure

- Regions & Zones (availability, latency)
- Importance of High Availability (HA)
- Geographic redundancy

<!-- 
Highlight the importance of selecting regions and zones to reduce latency, improve performance, and achieve redundancy.
-->

---
layout: image
image: /regions-maps_2x.png
---

<!--
Regions, Zones, Data Centres
-->

---

- 3 to 6 zones per region
- 1+ Data Centres per zone

```mermaid
graph TD;

    %% Define Zones within Region A
    subgraph ZoneA1
        direction TB
        DC_A1_1[Data Center A1-1]
        DC_A1_2[Data Center A1-2]
    end

    subgraph ZoneA2 [Zone A2]
        direction TB
        DC_A2_1[Data Center A2-1]
        %% Add more DCs if needed
    end

    subgraph ZoneA3 [Zone A3]
        direction TB
        DC_A3_1[Data Center A3-1]
        %% Add more DCs if needed
    end

    %% Link Region A to its Zones
    RegionA_Node[Region A] --> ZoneA1;
    RegionA_Node --> ZoneA2;
    RegionA_Node --> ZoneA3;


    %% Style the subgraph boxes for clarity (optional, depending on Mermaid renderer support)
    %% style Multi-Region Scope fill:#f9f,stroke:#333,stroke-width:2px,color:#333
    %% style RegionA fill:#ccf,stroke:#333,stroke-width:2px,color:#333
    %% style ZoneA1 fill:#cfc,stroke:#333,stroke-width:2px,color:#333

    %% Note: Styling commands might not render in all Mermaid viewers.
    %% The subgraph structure itself provides the visual grouping.

```

```mermaid
graph TD;

    %% Define Zones within Region B
    subgraph ZoneA1
        direction TB
        DC_A1_2[DC]
    end

    subgraph ZoneA2 [Zone A2]
        direction TB
        DC_A2_1[DC]
        DC_A2_2[DC]
    end

    subgraph ZoneA3 [Zone A3]
        direction TB
        DC_A3_1[DC]
    end

    subgraph ZoneA4
        direction TB
        DC_A4_2[DC]
        DC_A4_3[DC]
    end

    subgraph ZoneA5
        direction TB
        DC_A5_1[DC]
    end

    subgraph ZoneA6
        direction TB
        DC_A6_1[DC]
    end

    %% Link Region A to its Zones
    RegionB_Node[Region B] --> ZoneA1;
    RegionB_Node --> ZoneA2;
    RegionB_Node --> ZoneA3;
    RegionB_Node --> ZoneA4;
    RegionB_Node --> ZoneA5;
    RegionB_Node --> ZoneA6;


    %% Style the subgraph boxes for clarity (optional, depending on Mermaid renderer support)
    %% style Multi-Region Scope fill:#f9f,stroke:#333,stroke-width:2px,color:#333
    %% style RegionA fill:#ccf,stroke:#333,stroke-width:2px,color:#333
    %% style ZoneA1 fill:#cfc,stroke:#333,stroke-width:2px,color:#333

    %% Note: Styling commands might not render in all Mermaid viewers.
    %% The subgraph structure itself provides the visual grouping.

```
