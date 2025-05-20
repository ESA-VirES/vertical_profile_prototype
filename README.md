# Example STAC definition

## Rationale

As discussed this is an example STAC collection to allow coordination on all involved components: FedEO catalogue , View Server and MEEO viewer.

In principle following main tasks are understood by component:
* FedEO catalogue: Needs to provide a collection with the correct information
* View Server: Provides an interface to generate curtain previews
* MEEO Viewer: Can load the FedEO catalogue and visualize 3D vertical curtains


## Considerations

The main interface for all of these components is the STAC catalog, so what needs to be understood is, can:

* FedEO catalogue: Generate the collection as shown here?
  - Footprint/geometry generation is not trivial, specific logic was used to create the item geometries for the items, potentially another interface can be provided to also extract footprints for curtains?
  - There might also be an issue with Longitude values (go from 0 to 360; instead of -180 to 180) as well as antimeridian crossings, gaps in data, ...
* View Server: Generate previews as generated here?
  - Current previews generated using vires python client connecting to VirES for Aeolus 
  - Could be conceived as to be generated dynamically or pre-rendered as assets
  - Should a static value range be used (currently percentile range)?
  - Wind velocity values are base on horizontal line of sight and are dependent on pass direction (ascending/descending), should the values for previews be normalized to account for that?
  - Different flags and observation types: The current previews were generated using the validity flag and (for rayleigh) only observation type clear was used, should this somehow be tracked in the stac item?
* MEEO Viewer: Load the collection as present here and create a 3d visualization of it?
  - What complexity of geometry is acceptable?
  - Is the information available enough?
  - Could different asset previews be considered and made selectable (e.g. rayleigh vs mie)

This example focuses on the L2B product, which seems to be the "main" product, but each product type (L1A, L1B, L2A, L2B, L2C) has largely different aspects to be considered, e.g. different types of aggregation: measurement, observation, groups, ...
The Auxiliary products, are also even more different to these, so it would need to be defined if these are relevant. They can't be represented as vertical curtains in any case, more as points along the track.

## STAC item description

Example visualization using [Stac Browser](https://radiantearth.github.io/stac-browser/#/external/esa-vires.github.io/vertical_profile_prototype/collection.json)

* geometry: Is of type `LineString`, should allow any stac viewer to load and preview the footprint in 2D. Could be used as basis for creating the 3D geometry
* fixed_altitude_km: set to 24.0 km, can be adapted based on how the preview assets are rendered, this information could be used as part of generating the 3D geometry
* track_point_count_original: the footprints have been simplified, so potentially this information is important to keep in the stac item, e.g. 11270,
* track_point_count_simplified: simplified then is reduced to e.g. 196 coordinates,
* simplification_tolerance_deg: and a simplification tolerance of e.g. 0.3 has been applied, depending on how large and exact the items are needed this can be adapted (will significantly increase the size of the catalog)

In general multiple extensions could be used to improve the information available, i would propose to discuss mostly on the  important aspects for this exercise (creation of 3d curtains as visualization), but for the purpose of completion here are the other parameters added for now:

* id: Uses the product identifier
* platform: set to "Aeolus"
* instrument: set to "ALADIN"
* product: set to "L2B",
* wind_type: set to "Rayleigh" - the mie information could be an additional asset?, how would that be represented here?
* start_time: "2023-04-23T06:39:21.167387Z",
* end_time: "2023-04-23T08:09:54.188436Z",
* datetime: "2023-04-23T06:39:21.167387Z"

### Assets

The rendered curtain is referenced as asset, and has following structure:

```
"assets": {
    "curtain_preview": {
      "href": "https://esa-vires.github.io/vertical_profile_prototype/assets/AE_OPER_ALD_U_N_2B_20230423T063958_20230423T081032_0001.png",
      "type": "image/png",
      "title": "Aeolus Rayleigh Curtain Plot",
      "roles": [
        "visual"
      ]
    }
},
```

As quickly touched upon above, one might envision having multiple previews showing different aspects of the product (e.g. rayleigh and mie), if this is wanted i imagine we would need define the exact expected ids, to allow this interaction.
Potentially a more flexible possibility would be the MEEO Viewer can filter all assets of the role "visual" and allow selecting them in order to apply them as texture on the 3d geometry.
