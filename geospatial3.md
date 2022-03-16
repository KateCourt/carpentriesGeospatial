
![](/figures/Utm-zones-USA.svg)

<br>
<br>
<br>
<br>
<br>

## Raster bands
![](/figures/single_multi_raster.png)

<br>
<br>
<br>
<br>
<br>

## Missing data
![](/figures/rmd-01-demonstrate-no-data-black-ggplot-1.png)

<br>
<br>
<br>
<br>
<br>

![](/figures/rmd-01-demonstrate-no-data-ggplot-1.png)

<br>
<br>
<br>
<br>
<br>

![](/figures/rmd-01-napink-1.png)

<br>
<br>
<br>
<br>
<br>

### `NoDataValue` values

* For floating-point rasters, the figure `-3.4e+38` is a common default. 
* For integers, `-9999` is common. 

In some cases, other `NA` values may be more appropriate. An NA value should be:
 1. outside the range of valid values
 2. a value that fits the data type in use.

    <br>
    <br>
    <br>
    <br>
    <br>
 
 ## Exercise 1.1
Use the output from the `GDALinfo()` function to find out what `NoDataValue` is used for our `DSM_HARV` dataset.


`GDALinfo("data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif")`

<details>
<summary>Solution
</summary>

`NoDataValue` are encoded as -9999

</details>

<br>
<br>
<br>
<br>

## Bad data highlighting
![](/figures/rmd-01-demo-bad-data-highlighting-1.png)

<br>
<br>
<br>
<br>
<br>

## Exercise 1.2
Use `GDALinfo()` to determine the following about the `NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_DSMhill.tif` file:

1. Does this file have the same CRS as DSM_HARV?
1. What is the NoDataValue?
1. What is resolution of the raster data?
1. How large would a 5x5 pixel area be on the Earth’s surface?
1. Is the file a multi- or single-band raster?


Note: this is a hillshade, we'll learn more about them later in the workshop.

<details>
<summary>Solution
</summary>

1. If this file has the same CRS as DSM_HARV? Yes: UTM Zone 18, WGS84, meters.
1. What format NoDataValues take? -9999
1. The resolution of the raster data? 1x1
1. How large a 5x5 pixel area would be? 5mx5m How? We are given resolution of 1x1 and units in meters, therefore resolution of 5x5 means 5x5m.
1. Is the file a multi- or single-band raster? Single.

</details>

<br>
<br>
<br>
<br>


## Exercise 2.1 Plot using custom breaks

Create a plot of the Harvard Forest Digital Surface Model (DSM) that has:

1. Six classified ranges of values (break points) that are evenly divided among the range of pixel values.
1. Axis labels.
1. A plot title.


Hint: to create a title, use the `ggtitle()` function.

<details>
<summary>Solution
</summary>

```r
DSM_HARV_df <- DSM_HARV_df  %>%
               mutate(fct_elevation_6 = cut(HARV_dsmCrop, breaks = 6)) 

 my_col <- terrain.colors(6)

ggplot() +
    geom_raster(data = DSM_HARV_df , aes(x = x, y = y,
                                      fill = fct_elevation_6)) + 
    scale_fill_manual(values = my_col, name = "Elevation") + 
    ggtitle("Classified Elevation Map - NEON Harvard Forest Field Site") +
    xlab("UTM Easting Coordinate (m)") +
    ylab("UTM Northing Coordinate (m)") + 
    coord_quickmap()
```

</details>

<br>
<br>
<br>
<br>

## DSM vs DTM
![](/figures/lidarTree-height.png)

<br>
<br>
<br>
<br>
<br>

## Exercise 3.1 

Create a map of the San Joaquin Experimental Range field site (in Southern California) using the `data/NEON-DS-Airborne-Remote-Sensing/SJER/DSM/SJER_DSMhill_WGS84.tif` and `data/NEON-DS-Airborne-Remote-Sensing/SJER/DSM/SJER_dsmCrop.tif` files.

Reproject the data as necessary to make things line up!


<details>
<summary>Solution
</summary>

```
# import DSM
DSM_SJER <- raster("data/NEON-DS-Airborne-Remote-Sensing/SJER/DSM/SJER_dsmCrop.tif")
# import DSM hillshade
DSM_hill_SJER_WGS <-
raster("data/NEON-DS-Airborne-Remote-Sensing/SJER/DSM/SJER_DSMhill_WGS84.tif")

# reproject raster
DTM_hill_UTMZ18N_SJER <- projectRaster(DSM_hill_SJER_WGS,
                                  crs = crs(DSM_SJER),
                                  res = 1)

# convert to data.frames
DSM_SJER_df <- as.data.frame(DSM_SJER, xy = TRUE)

DSM_hill_SJER_df <- as.data.frame(DTM_hill_UTMZ18N_SJER, xy = TRUE)

ggplot() +
     geom_raster(data = DSM_hill_SJER_df, 
                 aes(x = x, y = y, 
                   alpha = SJER_DSMhill_WGS84)
                 ) +
     geom_raster(data = DSM_SJER_df, 
             aes(x = x, y = y, 
                  fill = SJER_dsmCrop,
                  alpha=0.8)
             ) + 
     scale_fill_gradientn(name = "Elevation", colors = terrain.colors(10)) + 
     coord_quickmap()
 ```    

</details>
     
<br>
     <br>
     <br>
     <br>
     
     
## Exercise 4.1
Data are often more interesting and powerful when we compare them across various locations. Let’s compare some data collected over Harvard Forest to data collected in Southern California. The NEON San Joaquin Experimental Range (SJER) field site located in Southern California has a very different ecosystem and climate than the NEON Harvard Forest Field Site in Massachusetts.

1. You should have the DSM (`DSM_SJER`) and DTM (`DTM_SJER`) data for the SJER site already loaded from the Plot Raster Data in R episode. 

1. Create a CHM from the two raster layers and check to make sure the data are what you expect by creating a historgram.
1. Plot the CHM from SJER.
1. Export the SJER CHM as a GeoTIFF.
1. Compare the vegetation structure of the Harvard Forest and San Joaquin Experimental Range.

<details>
<summary>Solution
</summary>

```
# Subtract two rasters
CHM_ov_SJER <- overlay(DSM_SJER,
                       DTM_SJER,
                       fun = function(r1, r2){ return(r1 - r2) })

# Convert output to a data frame
CHM_ov_SJER_df <- as.data.frame(CHM_ov_SJER, xy = TRUE)

# Create a historgram
ggplot(CHM_ov_SJER_df) +
    geom_histogram(aes(layer))

# Create a plot
 ggplot() +
      geom_raster(data = CHM_ov_SJER_df, 
              aes(x = x, y = y, 
                   fill = layer)
              ) + 
     scale_fill_gradientn(name = "Canopy Height", 
        colors = terrain.colors(10)) + 
     coord_quickmap()

# Export file

writeRaster(CHM_ov_SJER, "chm_ov_SJER.tiff",
            format = "GTiff",
            overwrite = TRUE,
            NAflag = -9999)
```

SJER has much shorter trees than HARV.

</details>
     
<br>
     <br>
     <br>
     <br>

## Exercise 5.1

View the attributes of this band. What are its dimensions, CRS, resolution, min and max values, and band number?

`RGB_band1_HARV`

<details>
<summary>Solution
</summary>

resolution: 0.25 x 0.25   
min: 0 max: 255   
band: 1 of 3
crs: +proj=utm +zone=18 +datum=WGS84 +units=m +no_defs

</details>
     
<br>
     <br>
     <br>
     <br>


## Image Stretch

![](/figures/imageStretch_dark.jpg)

<br>
     <br>
     <br>
     <br>

![](/figures/imageStretch_light.jpg)

<br>
     <br>
     <br>
     <br>

     
## Exercise 5.2

Let’s explore what happens with NoData values when working with RasterStack objects and using the plotRGB() function. We will use the `HARV_Ortho_wNA.tif` GeoTIFF file in the `NEON-DS-Airborne-Remote-Sensing/HARVRGB_Imagery/` directory.

1. View the files attributes using `GDALinfo("data/NEON-DS-Airborne-Remote-Sensing/HARV/RGB_Imagery/HARV_Ortho_wNA.tif")`. Are there `NoData` values assigned for this file?       
2. If so, what is the `NoData` value?
3. How many bands does it have?

 Load the multi-band raster file into R using the `stack()` function: `HARV_NA <- stack("data/NEON-DS-Airborne-Remote-Sensing/HARV/RGB_Imagery/HARV_Ortho_wNA.tif")`    

 Plot the object as a true color image: `plotRGB(HARV_NA, r= 1, g = 2, b = 3)`    

4. What happened to the black edges in the data?
5. What does this tell us about the difference in the data structure between `HARV_Ortho_wNA.tif` and `HARV_RGB_Ortho.tif` (R object `RGB_stack`). How can you check?

<details>
<summary>Solution
</summary>

1. Yes
2. -9999
3. 3
4. The black edges are not plotted.
1. Both data sets have NoData values, however, in the RGB_stack the NoData value is not defined in the tiff tags, thus R renders them as black as the reflectance values are 0. The black edges in the other file are defined as -9999 and R renders them as NA.

</details>

<br>
<br>
<br>
<br>
<br>

## Spatial extent

![](/figures/spatial_extent.png)

<br>
<br>
<br>
<br>

## Exercise 7.1 Subset spatial line objects
Subset out all `boardwalk` from the lines layer and plot it.

<details>
<summary>Solution
</summary>

```
boardwalk_HARV <- lines_HARV %>%
filter(TYPE == "boardwalk")

nrow(boardwalk_HARV)

ggplot() +
geom_sf(data = boardwalk_HARV, size = 1.5) +
ggtitle("NEON Harvard Forest Field Site", subtitle = "Boardwalks") +
coord_sf()
```

</details>

<br>
<br>
<br>
<br>
<br>

## Exercise 7.2 Subset spatial line objects 2
Subset out all `stone wall` features from the lines layer and plot it. For each plot, color each feature using a unique color.

<details>
<summary>Solution
</summary>

```
stoneWall_HARV <- lines_HARV %>% 
  filter(TYPE == "stone wall")
nrow(stoneWall_HARV)

ggplot() +
  geom_sf(data = stoneWall_HARV, aes(color = factor(OBJECTID)), size = 1.5) +
  labs(color = 'Wall ID') +
  ggtitle("NEON Harvard Forest Field Site", subtitle = "Stonewalls") + 
  coord_sf()
```

</details>

<br>
<br>
<br>
<br>
<br>

## Exercise 7.3 Plot line width by attribute
Let’s create another plot where we show the different line types with the following thicknesses:

1. woods road size = 6
2. boardwalks size = 1
3. footpath size = 3
4. stone wall size = 2


<details>
<summary>Solution
</summary>

```
levels(lines_HARV$TYPE)
line_width <- c(1, 3, 2, 6)
ggplot() +
  geom_sf(data = lines_HARV, aes(size = TYPE)) +
  scale_size_manual(values = line_width) +
  ggtitle("NEON Harvard Forest Field Site", subtitle = "Roads & Trails - Line width varies") + 
  coord_sf()
```

</details>

<br>
<br>
<br>
<br>
<br>


![](/figures/map_usa_different_projections.jpg)

<br>
<br>
<br>
<br>

## Exercise 9.1 Plot layers of spatial data

Create a map of the North Eastern United States as follows:

1. Import and plot`Boundary-US-State-NEast.shp`. Change the line size until you like it. Hint: import using this command: `NE.States.Boundary.US <- st_read("data/NEON-DS-Site-Layout-Files/US-Boundary-Layers/Boundary-US-State-NEast.shp")`
2. Layer the Fisher Tower (in the NEON Harvard Forest site) point location `point_HARV` onto the plot.


<details>
<summary>Solution
</summary>

```
ggplot() +
 geom_sf(data = NE.States.Boundary.US, size=1, color="gray18") +
 geom_sf(data=point_HARV, shape=19, color = "purple") +
 ggtitle("Fisher Tower location") + coord_sf()

```

</details>

<br>
<br>
<br>
<br>
<br>

## Exercise 10.1 
We want to add two phenology plots to our existing map of vegetation plot locations.

Import the .csv: `HARV/HARV_2NewPhenPlots.csv` . Hint: `newplot_locations_HARV <-
read.csv("data/NEON-DS-Site-Layout-Files/HARV/HARV_2NewPhenPlots.csv")`

Find the X and Y coordinate locations. Which value is X and which value is Y?
Hint: We need to use the format (Longitude, Latitude).

These data were collected in a geographic coordinate system (WGS84). Convert the dataframe into an sf object.
Hint: The US boundary data (`country_boundary_US`) we worked with previously is in a geographic WGS84 CRS.

Convert the data to a shapefile and plot the new points with the plot location points from the previous example (`plot_locations_sp_HARV`). Use a different symbol for the
2 new points by adding a `color` parameter to each `geom_sf` function. 

<details>
<summary>Solution
</summary>

Read in csv and look at columns
```

newplot_locations_HARV <-
  read.csv("data/NEON-DS-Site-Layout-Files/HARV/HARV_2NewPhenPlots.csv")
str(newplot_locations_HARV)

```
Use CRS from previous data

```
geogCRS <- st_crs(country_boundary_US)
geogCRS
```

Convert data to a shapefile and plot

```
newPlot_sp_HARV <- st_as_sf(newplot_locations_HARV, coords = c("decimalLon", "decimalLat"), crs = geogCRS)
ggplot() +
  geom_sf(data = plot_locations_sp_HARV, color = "orange") +
  geom_sf(data = newPlot_sp_HARV, color = "lightblue") +
  ggtitle("Map of All Plot Locations")
```

</details>

<br>
<br>
<br>
<br>
<br>

![](/figures/rmd-11-compare-data-extents-1.png)

<br>
<br>
<br>
<br>
<br>

## Exercise 11.1 Extract raster height values for plot locations
Use the plot locations object (`plot_locations_sp_HARV`) to extract an average tree height for the area within 
20m of each vegetation plot location in the study area. 
Because there are multiple plot locations, there will be multiple averages returned, 
so the `df = TRUE` argument should be used.

Print the values to the screen.

<details>
<summary>Solution
</summary>


```
mean_tree_height_tower <- extract(x = CHM_HARV,
                                  y = point_HARV,
                                  buffer = 20,
                                  fun = mean)
 
mean_tree_height_tower


```

</details>

<br>
<br>
<br>
<br>
<br>

![](/figures/UTM_zones_18-19.jpg)

<br>
<br>
<br>
<br>
<br>

## Dataframe without melt
```
   
      x       y      X005_HARV_ndvi_crop   X037_HARV_ndvi_crop ...
1  239430 4714350                4451                3106
```

## Dataframe with melt
```
         x       y            variable value
1   239430 4714350 X005_HARV_ndvi_crop  4451
2   239460 4714350 X005_HARV_ndvi_crop  5545
3   239490 4714350 X005_HARV_ndvi_crop  5251
```
<br>
<br>
<br>
<br>
<br>



![](/figures/rmd-12-ndvi-plots-1.png)



![](/figures/rmd-12-ndvi-plots-2.png)

<br>
<br>
<br>
<br>
<br>

