# ADR 001: Penggunaan PostgreSQL sebagai Data Warehouse

## Status
DITERIMA

## Konteks
Kita perlu memilih storage layer untuk data sensor pipeline. Pilihan:
1. **PostgreSQL**: RDBMS tradisional, on-prem/cloud
2. **Snowflake**: Cloud data warehouse, consumption-based pricing
3. **BigQuery**: Serverless, integration dengan GCP

## Keputusan
Kita akan menggunakan **PostgreSQL** karena:

### Pro
1. **Cost**: $0 untuk development (local), murah untuk production
2. **Simplicity**: Tim sudah familiar dengan SQL
3. **JSON Support**: Bisa simpan raw sensor data sebagai JSONB
4. **CTEs & Window Functions**: Critical untuk anomaly detection logic
5. **Transactional**: ACID compliance untuk data integrity

### Kontra
1. **Scalability**: Vertical scaling vs horizontal (Snowflake)
2. **Performance**: Untuk query besar, mungkin lebih lambat
3. **Management**: Self-managed vs serverless

### Konsekuensi
1. Perlu manage replication & backup manual
2. Partitioning diperlukan untuk data besar
3. Monitoring performance lebih hands-on

## Metadata
- **Tanggal**: 2026-01-23
- **Deciders**: Lead Data Engineer, CTO
- **Consulted**: Data Analyst Team, DevOps
