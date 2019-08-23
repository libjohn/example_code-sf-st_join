README
================
John Little
2019-08-23

<!-- README.md is generated from README.Rmd. Please edit that file -->

# example\_code-sf-st\_join

<!-- badges: start -->

<!-- badges: end -->

Exploring and demonstrating a few functions

  - `sf::st_join` with left and inner joins
  - `sf::st_join` with predicate functions such as `st_within` and
    `st_contains`
  - `mapview::mapview`

### Notes

  - [Notes on EPSG codes and
    projections](https://guides.library.duke.edu/r-geospatial/CRS#s-lib-ctab-21326020-2)
  - [Notes on sf and mapview](https://map-rfun.library.duke.edu/)

<!-- end list -->

``` r
# Examples of relevant code


# read in XY Coordinates and assign Coordinates to degrees
sbux_drm_sf <- st_as_sf(sbux_drm,
                        coords = c("Longitude", "Latitude"),
                        crs = 4326)

                        
# read in shapefiles
durham_blocks <- tracts(state = "NC", 
                        county = "Durham", 
                        class = "sf", 
                        year = 2012)

# Transform to a projection
durham_blocks <- st_transform(durham_blocks,
                              st_crs(2264))

sbux_drm_sf <- st_transform(sbux_drm_sf,
                            st_crs(durham_blocks)) 

# joins
shp_with_bux <- st_join(durham_blocks, sbux_drm_sf, join = st_contains, left = FALSE)

bux_within_shp <- st_join(sbux_drm_sf, durham_blocks, left = TRUE, join = st_within)

# mapview
mapview(durham_blocks, col.regions = "grey",
        legend = FALSE,
        popup = popupTable(st_drop_geometry(durham_blocks), 
                           zcol = c(4,5),
                           row.numbers = FALSE,
                           feature.id = FALSE)) +
  mapview(shp_with_bux, 
          zcol = "NAME",
          layer.name = "Tract Name") +
  mapview(bux_within_shp,
          legend = FALSE,
          cex = 0,
          #cex = "AWATER",
          lwd = 3,
          alpha = .5,
          color = "red",
          zcol = "Store Number",
          popup =
            popupTable(st_drop_geometry(bux_within_shp),
                             zcol = c(1,2,3,5,10,11),
                           row.numbers = FALSE,
                           feature.id = FALSE))
```

See more details in the 01\_explore\_st\_join.\[Rmd|html\]
