# SIEM & Threat Hunting Lab: Email Security Analytics in Splunk

An interactive, production-ready monitoring dashboard developed within **Splunk Cloud (Dashboard Studio)**. This project simulates a corporate Security Operations Center (SOC) environment to analyze inbound phishing traffic, monitor delivery filter efficiency, and aggregate malicious infrastructure indicators of compromise (IoCs).

---

## 🛠️ Project Architecture & Features

- **Interactive Visualizations**: Implemented structural layouts using the modern **Splunk Grid system** layered in standard dark operational mode (SOC-standard dark theme).
- **Email Volume & Delivery Status Panel**: A dynamic Stacked Bar Chart mapping delivery efficiency ("Bloqueado pelo Filtro" vs. "Entregue na Inbox") across specific corporate user identities.
- **Top Inbound Malicious IPs & Targets Panel**: A highly structured analytical table capturing external attacker source IPs (`src_ip`), linking them directly to targeted internal employee vectors, and ranking them dynamically by total attack volume.

---

## 🔍 Advanced SPL Engineering & Syntax Covered

This project demonstrates the execution of multi-layered data ingestion simulation and field evaluation using advanced **Splunk Processing Language (SPL)** pipelines.

### Key Search Frameworks Implemented:

1. **Synthetic Event Ingestion & Windowing**:
   Utilized `makeresults` combined with iterative `streamstats` functions to engineer precise, control-isolated data rows inside temporary runtime memory without requiring disk storage footprints.

2. **Multidimensional Array Mapping**:
   Deployed the `eval` command coupled with native array functions like `mvindex()` and `split()` to programmatically map static string matrices across index configurations.

3. **Mathematical Array Modulo Calculation**:
   Applied mathematical modulation operators (`%`) directly inside the evaluation pipeline to cyclically cycle records through the dynamic target names array.

4. **Data Pivoting & Formatting**:
   Utilized the `chart count over ... by ...` and `stats` execution blocks to reshape multiple data streams into unified tables ready for immediate dashboard ingestion.

---

## 💻 SPL Source Queries

### 1. Panel: Email Volume & Delivery Status per Corporate Target
```splunk
| makeresults count=30
| streamstats count as row
| eval idx = (row - 1) % 3
| eval email_status=mvindex(split("Bloqueado pelo Filtro,Entregue na Inbox,Bloqueado pelo Filtro", ","), idx)
| eval user_idx = (row - 1) % 5
| eval usuario=mvindex(split("ana.silva,carlos.eduardo,julia.costa,bruno.santos,marina.oliveira", ","), user_idx)
| chart count over usuario by email_status
```

### 2. Panel: Top Inbound Malicious IPs & Targets
```splunk
| makeresults count=50
| streamstats count as row
| eval ip_idx = (row - 1) % 4
| eval src_ip = mvindex(split("192.168.1.50,10.0.0.12,45.89.23.11,185.220.101.5", ","), ip_idx)
| eval user_idx = (row - 1) % 5
| eval usuario = mvindex(split("ana.silva,carlos.eduardo,julia.costa,bruno.santos,marina.oliveira", ","), user_idx)
| stats count by src_ip, usuario
| sort - count
| rename src_ip as "Source IP", usuario as "Target User", count as "Attack Attempts"
```

---

## 🎯 Professional Impact & Use Case

This deployment represents an operational blueprint for Blue Team specialists. By monitoring this analytical layer, a Security Operations team can instantly determine:
1. **Who** the high-value targeted employees are inside an enterprise network.
2. **The Efficiency** of existing boundary defense filters.
3. **Attacker Source Infrastructure** patterns to orchestrate automated blocklists across active perimeter Firewalls.
