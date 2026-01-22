# 📐 Arsitektur Pipeline: Monitoring Sensor Pipa Minyak

## 🎯 Tujuan Bisnis
Membangun sistem monitoring real-time untuk sensor pipa minyak dengan kemampuan:
1. **Deteksi anomaly** suhu dan tekanan secara otomatis
2. **Pelaporan real-time** untuk tim operasional
3. **Historical analysis** untuk preventive maintenance
4. **Alerting sistem** ketika threshold terlewati

## 🏗️ Arsitektur Teknis

### Komponen Utama
Data Sources → Redis Stream → Airflow → PostgreSQL → dbt → Grafana
↑ ↓ ↓ ↓
Simulator Bronze Silver/Gold Dashboard


### Pilihan Teknologi & Justifikasi

#### 1. **Redis sebagai Streaming Layer**
- **Kenapa?**: Data sensor masuk dengan volume tinggi (10k events/detik)
- **Keuntungan**: Low-latency, pub/sub pattern, persistence opsional
- **Alternatif yang dipertimbangkan**: Kafka (lebih kompleks), RabbitMQ (kurang scalable)

#### 2. **PostgreSQL sebagai Data Warehouse**
- **Kenapa?**: 
  - Data relasional (sensor → pipeline → anomaly)
  - Support JSON untuk data raw
  - Familiaritas tim
  - CTEs & window functions untuk anomaly detection
- **Trade-off**: Bukan data lake, tapi cocok untuk volume data ini

#### 3. **dbt untuk Transformasi**
- **Kenapa?**: 
  - Version-controlled SQL
  - Testing built-in
  - Documentation generation
  - Modular transformations
- **Critical path**: Bronze → Silver → Gold

#### 4. **Airflow untuk Orkestrasi**
- **Kenapa?**:
  - Schedule pipeline setiap 5 menit
  - Retry logic untuk transient failures
  - Monitoring task-level
  - Community besar

#### 5. **Grafana untuk Visualization**
- **Kenapa?**:
  - Real-time dashboards
  - Alert integration
  - Team familiarity

### Data Flow
1. **Bronze Layer**: Raw data, append-only, schema validation
2. **Silver Layer**: Cleaned, validated, business entities
3. **Gold Layer**: Business-ready, star schema, anomaly flags

### Anomaly Detection Logic
- **Suhu**: > 100°C atau < 50°C = ANOMALY
- **Pressure**: > 150 psi atau < 80 psi = ANOMALY
- **Rate of Change**: Δ > 10°C/menit = ANOMALY

### Risk Mitigation
1. **Data Loss**: Redis persistence + WAL PostgreSQL
2. **Pipeline Failure**: Airflow retry + dead letter queue
3. **Quality**: dbt tests + data contracts
