# Constraint Sources and Query Patterns

Sources for the Stage 5 property screen, in the order the screen runs them. Public REST services can be queried directly from a parcel centroid or address; jurisdiction zoning/GP layers usually require browsing the local viewer (Claude in Chrome) because they are not standardized. Where the Acres report already carries a layer, use it and treat the public service as a cross-check only.

> **Maintenance note.** Agency endpoints and viewer URLs change. Treat the base URLs below as entry points to verify against the live service, not as guaranteed-stable addresses. When a query fails, confirm the current service URL before concluding the layer is absent.

## 5.1 Zoning and General Plan — run first

| Layer | Where | Notes |
|---|---|---|
| Zoning designation | Jurisdiction parcel viewer / zoning map | City viewer if incorporated, else county. Often an ArcGIS REST layer; otherwise browse the viewer. |
| General Plan land use designation | Jurisdiction GP land use map | Drives what zoning can be brought into conformance with; sometimes diverges from current zoning. |
| Development standards | Municipal/county zoning code for the designation | Density/units-per-acre, FAR, height, setbacks, lot coverage, parking, permitted/conditional uses. |

Both designations gate Stage 5.4 — identify them before scanning levers.

## 5.3 Environmental constraints

| Layer | Source | Query pattern | In Acres? |
|---|---|---|---|
| Topography | Acres report (slope, contours); USGS for detail | Read from report | Yes |
| FEMA flood (NFHL) | Acres report; NFHL service (cross-check) | Identify at centroid → zone, panel, BFE | Yes (cross-check only) |
| Wetlands (NWI) | USFWS Wetlands service (see recipe) | Live envelope query → feature count, type | No — required live query |
| Aquatic resources (CARI) | SFEI CARI FeatureServers (see recipe) | Live envelope query → mapped streams/wetlands | No — required live query |
| Habitat / HCP-NCCP | Jurisdiction or CDFW/USFWS overlay where adopted | Check adopted plan area + covered-species fees/process | No |

NWI and CARI are a **required live query**, not optional (SKILL.md Stage 5.3). For each, record the feature **count** in the envelope, the feature **type/name** where the service returns it, and the **centroid + envelope coordinates and service URLs used**. A non-zero count is a screening flag.

### NWI/CARI query recipe (verified 2026-06-08)

1. **Centroid.** Geocode the street address with the US Census geocoder (JSON): `https://geocoding.geo.census.gov/geocoder/locations/onelineaddress?address=<ADDRESS>&benchmark=Public_AR_Current&format=json`
2. **Envelope.** Build a box ~±0.0035° around the centroid (≈600 × 780 m, covers a ~20-ac parcel plus margin): `xmin,ymin,xmax,ymax` in WGS84 (`inSR=4326`).
3. **Count first, then attributes.** Use `returnCountOnly=true` for the screening flag; only pull `outFields` when you need to characterize features.
4. **Keep query URLs short.** The fetch tool caps URL length (~250 chars). Use `geometryType=esriGeometryEnvelope` (compact) rather than a point + `distance`/`units` buffer, and drop optional params — long URLs return `cowork_web_fetch_url_too_long`.

**Endpoints (verified working):**

- **NWI (USFWS Wetlands):** `https://fwspublicservices.wim.usgs.gov/wetlandsmapservice/rest/services/Wetlands/MapServer/0/query`
  Count example: `…/0/query?geometry=<xmin>,<ymin>,<xmax>,<ymax>&geometryType=esriGeometryEnvelope&inSR=4326&returnCountOnly=true&f=json`
  **Quirk:** returns counts fine but **400s on `outFields`** through the current fetch proxy. If attributes are needed and it keeps 400-ing, record the count and defer classification to the ISR rather than burning retries.
- **CARI streams (SFEI, ds2836):** `https://services2.arcgis.com/Uq9r85Potqm3MfRV/arcgis/rest/services/biosds2836_fnu/FeatureServer/0/query`
- **CARI wetlands (SFEI, ds2835):** `https://services2.arcgis.com/Uq9r85Potqm3MfRV/arcgis/rest/services/biosds2835_fpu/FeatureServer/0/query`
  These accept `outFields=*` cleanly; useful fields: `name`, `clicklabel`, `major_class`, `CRAM_wetland_type`, `anthropogenic_modifier`. (On the Toor parcel these returned the "McCall Ditch" as an artificial/unnatural drainage.)
- **FEMA NFHL (cross-check only; Acres carries flood):** `https://hazards.fema.gov/gis/nfhl/rest/services/public/NFHL/MapServer/28/query`

**Reporting rule.** Counts and types from these services are **mapped indicators**, not delineations. On-parcel extent needs the parcel polygon (not just the centroid envelope), and CWA §404 / §401 / CDFW §1602 jurisdiction is a field + ISR determination. Never state a mapped feature as field-confirmed.

**Maps.** Figure-quality NWI/CARI exhibits are a paid ISR deliverable (SKILL.md Stage 5.3); in the screen, record presence, counts, features, centroid/envelope, and URLs to tee up the ISR figure step.

## Jurisdiction quick reference — zoning and General Plan

County GIS viewers generally cover **unincorporated areas only**. A parcel inside an incorporated city is governed by that city's zoning, not the county's — confirm which jurisdiction controls before using a county source. Every viewer is a research tool, not a legal determination; confirm zoning with the agency for paid work.

### Primary — most Cox work

**City of Sacramento**
- GIS map tools / zoning lookup: https://www.cityofsacramento.org/GIS/Map-tools
- Zoning code: Sacramento City Code Title 17 (Planning and Development Code)

**County of Sacramento** (unincorporated)
- GIS open data hub (parcels, zoning, REST layers): https://data.saccounty.gov/ — zoning layer: https://data.saccounty.gov/maps/sacramentocounty::zoning
- Planning and community maps: https://planning.saccounty.gov/Pages/PlanningandCommunityMaps.aspx
- Zoning code: Sacramento County Zoning Code

**Placer County** (unincorporated)
- GIS & online maps: https://www.placer.ca.gov/2842/GIS-and-Online-Maps — interactive parcel map: https://www.placer.ca.gov/8012/Interactive-Map
- Open data hub (REST layers): https://gis-placercounty.opendata.arcgis.com/
- Zoning code: Placer County Code (Zoning Ordinance)

### Secondary — occasional work

**City of Los Angeles** (use for City parcels, e.g., Atwater Village)
- ZIMAS — zoning, land use, overlays, permit history: https://zimas.lacity.org/ — zoning search: https://planning.lacity.gov/zoning/zoning-search
- Zoning code: LAMC Chapter 1 (original) / Chapter 1A (new zoning code, phasing in from 2025)

**LA County** (unincorporated)
- Regional Planning interactive GIS apps (zoning/land use; includes an SB 330 preliminary-application layer): https://planning.lacounty.gov/maps-and-gis/interactive-gis-web-mapping-apps/
- Zoning code: LA County Code Title 22

**Alameda County** (unincorporated)
- Unincorporated zoning viewer: https://acpwa.maps.arcgis.com/apps/View/index.html?appid=4a648cb409d744b8a4f645e6e35fe773
- Open data hub (GeoServices / WMS / WFS APIs): https://data.acgov.org/
- Zoning code: Alameda County General Ordinance Code Title 17

**Solano County** (unincorporated)
- Parcel viewer: https://solanocountygis.com/portal/apps/webappviewer/index.html?id=b2a40316824143fc9f361d5d81c51a7a — General Plan & zoning maps: https://solanocountygis.com/portal/apps/instant/portfolio/index.html?appid=91c21740c75f41a5a1d9202b53ffa522
- Zoning guide ("What can I do on my property?"): https://www.solanocounty.gov/government/resource-management/planning-services/development-planning/look-zoning-faq/what-can-i-do-my-property
- Zoning code: Solano County Code Chapter 28

**El Dorado County** (unincorporated)
- GIS viewer: https://see-eldorado.edcgov.us/ugotnet/
- Parcel Data lookup (APN returns zoning, General Plan land use, flood zone, rare-plant mitigation): https://www.eldoradocounty.ca.gov/Land-Use/Planning-and-Building/Planning-Division/Parcel-Data-Information
- Zoning code: El Dorado County Zoning Ordinance (County Code Title 130)

> Several of these hubs — Sacramento County, Placer, Alameda, and the Solano ReGIS consortium — expose ArcGIS REST / GeoServices endpoints, so this layer can eventually support programmatic identify-by-parcel queries the same way FEMA, NWI, and CARI do. Add new jurisdictions here as Cox picks up work in them.

## Practical notes

- Derive a parcel centroid (or polygon) from the APN once, then reuse it across the point/identify queries.
- For jurisdiction zoning/GP, prefer the parcel viewer's own popup over a generic search — designations are authoritative there.
- FEMA and topo come free with the Acres report; spend query effort on NWI, CARI, and the jurisdiction zoning/GP, which Acres does not cover comprehensively.
- Note any data gaps explicitly in the findings rather than implying full coverage.
