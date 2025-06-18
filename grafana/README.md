# Grafana Healthcare Claims Dashboard

This directory contains a Dockerized Grafana instance configured to visualize healthcare claims data from a CSV file.

## Prerequisites

- Docker and Docker Compose installed on your system
- Port 3000 available (for Grafana)
- Port 8080 available (for CSV file server)

## Quick Start

1. **Clone the repository** (if you haven't already):
   ```bash
   git clone <repository-url>
   cd self_insured/grafana
   ```

2. **Ensure the CSV data file is present**:
   - The file `data/mhc_with_tic.csv` should already be in the repository
   - If not, place your CSV file at `data/mhc_with_tic.csv`

3. **Start the services**:
   ```bash
   docker-compose up -d
   ```

   This will:
   - Start Grafana on http://localhost:3000
   - Start a Python HTTP server on http://localhost:8080 to serve the CSV file
   - Install the CSV datasource plugin
   - Configure the datasource and dashboards automatically

4. **Access Grafana**:
   - Open http://localhost:3000 in your browser
   - Login with:
     - Username: `admin`
     - Password: `admin`

5. **View the dashboards**:
   - Click on "Dashboards" in the left sidebar
   - Select "Healthcare Claims Analysis (CSV)" or "MHC Simple Test"

## Directory Structure

```
grafana/
├── data/                           # CSV data files
│   └── mhc_with_tic.csv           # Healthcare claims data
├── dashboards/                     # Dashboard JSON files
│   ├── mhc-dashboard.json         # Main healthcare dashboard
│   └── mhc-simple.json            # Simple test dashboard
├── provisioning/                   # Grafana provisioning configs
│   ├── dashboards/
│   │   └── dashboard-provider.yml  # Dashboard provisioning config
│   └── datasources/
│       └── csv-datasource.yml      # CSV datasource config
├── docker-compose.yml              # Docker compose configuration
└── README.md                       # This file
```

## Managing the Services

**Check service status:**
```bash
docker-compose ps
```

**View logs:**
```bash
docker-compose logs -f
```

**Stop services:**
```bash
docker-compose down
```

**Restart services:**
```bash
docker-compose restart
```

**Stop and remove all data:**
```bash
docker-compose down -v
```

## Troubleshooting

### "No Data" in panels
1. Check that the CSV file exists at `data/mhc_with_tic.csv`
2. Verify the CSV server is running: `curl http://localhost:8080/mhc_with_tic.csv | head`
3. In Grafana, go to Connections → Data sources → MHC Claims CSV → Save & Test
4. Check the time range selector in the dashboard (data spans 2023-2024)

### Cannot access Grafana
1. Check if port 3000 is already in use: `sudo lsof -i :3000`
2. Check Docker logs: `docker-compose logs grafana`
3. Ensure Docker is running: `docker ps`

### CSV file not loading
1. Check file permissions: `ls -la data/`
2. Test CSV server: `curl -I http://localhost:8080/mhc_with_tic.csv`
3. Check CSV server logs: `docker-compose logs csv-server`

## Customization

### Adding new CSV files
1. Place the CSV file in the `data/` directory
2. Update the datasource configuration in `provisioning/datasources/csv-datasource.yml`
3. Restart Grafana: `docker-compose restart grafana`

### Creating new dashboards
1. Create dashboards in Grafana UI
2. Export as JSON (Dashboard settings → JSON Model)
3. Save to `dashboards/` directory
4. Restart Grafana to auto-load

### Changing default credentials
Edit `docker-compose.yml` and modify:
```yaml
- GF_SECURITY_ADMIN_USER=your_username
- GF_SECURITY_ADMIN_PASSWORD=your_password
```

## Data Notes

The healthcare claims CSV contains the following key fields:
- Member ID, ZIP codes, and state information
- Plan descriptions and subgroups
- Provider information (ID, NPI, address, network status)
- Diagnosis codes (ICD-10)
- Procedure codes and service categories
- Incurred dates (format: MM/DD/YYYY)
- Billed amounts and provider rates

The dashboards visualize:
- Claims by service category
- Total billed amounts
- Claims over time
- Geographic distribution by state
- Top diagnoses
- Average billing by plan type