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
  - 


## STAC item description
* geometry: Is of type `LineString`, should allow any stac viewer to load and preview the footprint in 2D. Could be used as basis for creating the 3D geometry
* fixed_altitude_km: set to 24.0 km, can be adapted based on how the preview assets are rendered, this information could be used as part of generating the 3D geometry
* track_point_count_original: the footprints have been simplified, so potentially this information is important to keep in the stac item, e.g. 11270,
* track_point_count_simplified: simplified then is reduced to e.g. 196 coordinates,
* simplification_tolerance_deg: and a simplification tolerance of e.g. 0.3 has been applied, depending on how large and exact the items are needed this can be adapted (will significantly increase the size of the catalog)

In general multiple extensions could be used to improve the information available, i would propose to discuss mostly on the for this exercise (creation of 3d curtains as visualization) important aspects, but for the purpose of completion here are the other parameters added for now:

* id: Uses the product identifier
* platform: set to "Aeolus"
* instrument: set to "ALADIN"
* product: set to "L2B",
* wind_type: set to "Rayleigh" - the mie information could be an additional asset?, how would that be represented here?
* start_time: "2023-04-23T06:39:21.167387Z",
* end_time: "2023-04-23T08:09:54.188436Z",
* datetime: "2023-04-23T06:39:21.167387Z"