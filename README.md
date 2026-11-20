# Cloud Extension Specification

- **Title:** Cloud
- **Identifier:** <https://stac-extensions.github.io/cloud/v1.0.0/schema.json>
- **Field Name Prefix:** `cloud`
- **Scope:** Item
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/README.md#extension-maturity):** Proposal

The STAC Cloud Extension describes cloud and atmospheric obscuration measurements for remotely sensed Earth observation data. It complements the [Earth Observation (EO) Extension](https://github.com/stac-extensions/eo), and can be used with imagery from Landsat, Sentinel-2, MODIS, VIIRS, and other current or future missions.

The EO Extension's `eo:cloud_cover` remains the authoritative STAC cloud-cover percentage: the percentage of the scene covered by clouds. The `cloud:*` fields add optional detail about clear surface, cloud types, shadows, snow and ice, haze, smoke, classified areas, and the methods and source masks used to produce those measurements. The extension does not require every field to be present.

## Examples

- [Catalog Example](examples/catalog.json)
- [Collection Example](examples/collection.json)
- [Item Example](examples/item.json)
- [JSON Schema](json-schema/schema.json)

## Item Properties

Percentages are expressed from 0 to 100. Area measurements and the percentage denominator are in square kilometers (`km^2`). `cloud:reference_area` is the valid area used to calculate the classification percentages; it may be smaller than the full footprint because invalid or unobserved pixels are excluded.

| Field | Type | Units | Description |
| --- | --- | --- | --- |
| `eo:cloud_cover` | number | % | Authoritative EO Extension percentage of the scene covered by clouds. |
| `cloud:clear` | number | % | Percentage of valid pixels classified as clear surface. |
| `cloud:clear_area` | number | km^2 | Area of clear visible surface. |
| `cloud:cloud_area` | number | km^2 | Area classified as cloud. |
| `cloud:shadow` | number | % | Percentage classified as cloud shadow. |
| `cloud:shadow_area` | number | km^2 | Area classified as cloud shadow. |
| `cloud:cirrus` | number | % | Percentage classified as cirrus cloud. |
| `cloud:cirrus_area` | number | km^2 | Area classified as cirrus cloud. |
| `cloud:snow_ice` | number | % | Percentage classified as snow or ice. |
| `cloud:snow_ice_area` | number | km^2 | Area classified as snow or ice. |
| `cloud:haze` | number | % | Percentage classified as haze or aerosol obscuration. |
| `cloud:haze_area` | number | km^2 | Area classified as haze or aerosol obscuration. |
| `cloud:smoke` | number | % | Percentage classified as smoke contamination. |
| `cloud:smoke_area` | number | km^2 | Area classified as smoke contamination. |
| `cloud:reference_area` | number | km^2 | Total valid area used as the denominator for percentages. |
| `cloud:algorithm` | string | - | Cloud or atmospheric obscuration detection/classification algorithm. |
| `cloud:algorithm_version` | string | - | Version of the algorithm. |
| `cloud:mask_type` | string | - | Source mask type. The schema accepts `qa_band`, `pixel_qa`, `qa_pixel`, `fmask`, `cfmask`, `scl`, `probability`, or `custom`. |
| `cloud:confidence` | string | - | Overall confidence assessment: `low`, `medium`, or `high`. |
| `cloud:classes` | object | - | Mapping of source classification values to class meanings. Keys are source values and values are class-name strings. |
| `cloud:modis` | object | - | Optional MODIS-specific measurements, preserving the native parameter names and values. See [MODIS parameters](#modis-parameters). |

Classification percentages and areas should describe the same valid footprint and source classification. The extension does not require classifications to be mutually exclusive or their percentages to sum to 100; document the algorithm's interpretation when categories overlap. Do not substitute a derived clear-sky or obscuration percentage for `eo:cloud_cover`.

## Using the Extension

Include the Cloud Extension schema URI in an Item's `stac_extensions` array when using `cloud:*` fields. Include the EO Extension URI when using `eo:cloud_cover`. The example Item shows both extensions and representative MODIS-style metadata.

## MODIS Parameters

The MODIS reference describes Collection 6.1 Level-3 daily cloud products (MOD08/MYD08), derived from MOD06/MYD06 Level-2 products, as well as a monthly Aqua cloud droplet number concentration product. The Cloud Extension exposes these as optional, grouped native parameters in `cloud:modis`:

| `cloud:modis` group | MODIS content |
| --- | --- |
| `fractions` | Total cloud fraction (all/day/night); cirrus and high-cloud fraction; liquid, ice, undetermined, and combined phase fractions for cloudy/partly cloudy, single-layer, and multilayer retrievals; and retrievals using 1.6 µm, 3.7 µm, and combined 1.6/2.1 µm bands. |
| `cloud_top` | Cloud-top temperature, pressure, and geopotential height (all/day/night where available). |
| `water_path` | Liquid, ice, and undetermined cloud water path for cloudy/partly cloudy and single-layer cases, including 1.6 µm, 3.7 µm, and combined 1.6/2.1 µm retrievals. |
| `optical_properties` | Cloud optical thickness and particle effective radius by phase, partly cloudy/single-layer case, and the same spectral retrieval variants; effective emissivity (all/day/night); and cirrus reflectance. |
| `angles` | Solar and sensor zenith and azimuth angles. |
| `surface_fractions` | Ocean, coast, desert, and land fractions when available in the source product, including the cloud-top product's `ctp_*fraction_*` fields. |
| `joint_histograms` | Joint histograms for cloud-top temperature/pressure/height/emissivity/fraction and optical thickness/effective radius/temperature/pressure relationships. Each histogram carries its own axis bin edges and 2D count matrix. |
| `cloud_droplet_number_concentration` | Monthly mean cloud droplet number concentration parameters. |

Within the scalar groups, use the exact source variable names as keys. Preserve the source's `_sdev`, `_n`, and `_error` variables as separate numeric entries; they represent standard deviation, sample count, and reported retrieval error respectively, and are not interchangeable. `PCL` in a source variable name denotes a partly cloudy case. Histograms use the exact source histogram variable names as keys, with `x_bin_edges`, `y_bin_edges`, and `counts`; retain the source axis order and bin definitions. For monthly cloud droplet number concentration, use the variable name provided by the source product.

The parameter listings do not provide a complete unit/scale definition for every variable. Preserve source values and units, and consult the MODIS product guide and metadata before converting, aggregating, or comparing values. MODIS native names belong only under `cloud:modis`; the mission-neutral Cloud fields remain available for other sensors. Do not assume a MODIS fraction is equivalent to `eo:cloud_cover` without verifying its definition, scale, and spatial/temporal support.

The example Item's MODIS-specific values and histogram are synthetic illustrations, not MODIS observations.

Reference parameter lists:

- [Cloud fraction](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/docs-atmo/modis-c006-cloud-fraction-parameter-list.txt)
- [Cloud water path](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/docs-atmo/modis-c006-cloud-water-paths-parameter-list.txt)
- [Cloud top parameters](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/docs-atmo/modis-c006-cloud-top-parameter-list.txt)
- [Cloud radiative properties](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/docs-atmo/modis-c006-cloud-radiative-properties-parameter-list.txt)
- [Cloud joint histograms](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/docs-atmo/modis-c006-cloud-histogram-parameter-list.txt)
- [MODIS cloud properties and product references](https://www.geo.uni-hamburg.de/en/service/icdc/data/atmosphere/modis-cloud-properties.html)

## Contributing

Contributions are subject to the [STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md). Please follow the [STAC Specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md). Ensure example JSON is valid and that Cloud Extension fields conform to [the schema](json-schema/schema.json).
