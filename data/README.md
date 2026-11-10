# Data sources and generated outputs

The notebooks download the inputs and write local caches, quality reports, and DuckDB files under this directory. Figures in the main README describe the saved project runs.

| Source | Input used |
| --- | --- |
| [Clean Energy Regulator NGER reporting data](https://cer.gov.au/markets/reports-and-data/nger-reporting-data-and-registers) | Electricity-sector emissions and generation data, financial years 2014–15 through 2023–24. The batch notebook uses the CER data API and records the dataset ID for each year. |
| [Clean Energy Regulator large-scale renewable energy data](https://cer.gov.au/markets/reports-and-data/large-scale-renewable-energy-data) | CSV registers for accredited, committed, and probable power stations and projects. These are current registers. |
| [ABS Data by Region, 2011–24](https://www.abs.gov.au/methodologies/data-region-methodology/2011-24) | Population and People workbook, Table 1. The notebook downloads the workbook directly. |
| [ABS Data API](https://www.abs.gov.au/about/data-services/application-programming-interfaces-apis/data-api) | Quarterly estimated resident population (`ERP_Q`) supplements missing state-year values. |
| [OpenElectricity API](https://docs.openelectricity.org.au/) | NEM facility and unit metadata, five-minute power and emissions, and regional price and demand. The configured window is 1 May 2026 00:00 through 8 May 2026 00:00 exclusive in network-local time. |

## Regenerate

- Run the [batch notebook](../batch_pipeline/energy_batch_pipeline.ipynb). `batch/Task1_output/` holds the downloads, `batch/Task2_output/` holds cleaned tables and matching diagnostics, and `batch/Task3_output/` holds the coordinate-enriched CSV. Stage 4 writes the staging database under `batch/Task4_output/`.
- For coordinates, provide your own or authorised Nominatim-compatible search endpoint in `NOMINATIM_SEARCH_URL`. The pipeline first looks up the facility with its postcode and state, then tries postcode and state alone. Locations from that fallback are approximate. [OpenStreetMap Nominatim usage policy](https://operations.osmfoundation.org/policies/nominatim/)
- Run the [streaming notebook](../streaming_pipeline/nem_mqtt_replay_dashboard.ipynb) with the retrieval flag enabled to create the raw facility, time-series, and market CSVs under `streaming/Task1_output/`. Later stages use the local cache. Stage 2 writes the consolidated dataset and dimensions under `streaming/Task2_output/`, Stage 4 extends a copy of the batch staging database under `streaming/Task4_output/`, and the publisher stores its replay cursor under `streaming/Task3_output/`.
