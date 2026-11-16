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

Classification percentages and areas should describe the same valid footprint and source classification. The extension does not require classifications to be mutually exclusive or their percentages to sum to 100; document the algorithm's interpretation when categories overlap. Do not substitute a derived clear-sky or obscuration percentage for `eo:cloud_cover`.

## Using the Extension

Include the Cloud Extension schema URI in an Item's `stac_extensions` array when using `cloud:*` fields. Include the EO Extension URI when using `eo:cloud_cover`. The example Item shows both extensions and representative Landsat-style metadata.

## Contributing

Contributions are subject to the [STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md). Please follow the [STAC Specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md). Ensure example JSON is valid and that Cloud Extension fields conform to [the schema](json-schema/schema.json).
