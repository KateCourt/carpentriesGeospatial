
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
 
 ## Exercise 1.1
Use the output from the `GDALinfo()` function to find out what `NoDataValue` is used for our `DSM_HARV` dataset.

todo update with file location

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

1. You should have the DSM and DTM data for the SJER site already loaded from the Plot Raster Data in R episode.) 
1. Don’t forget to check the CRSs and units of the data.

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
     
## Exercise 5.2

Let’s explore what happens with NoData values when working with RasterStack objects and using the plotRGB() function. We will use the `HARV_Ortho_wNA.tif` GeoTIFF file in the `NEON-DS-Airborne-Remote-Sensing/HARVRGB_Imagery/` directory.

1. View the files attributes using `GDALinfo("data/NEON-DS-Airborne-Remote-Sensing/HARV/RGB_Imagery/HARV_Ortho_wNA.tif")`. Are there `NoData` values assigned for this file?
2.If so, what is the `NoData` value?
3. How many bands does it have?

 Load the multi-band raster file into R using the `stack()` function: `HARV_NA <- stack("data/NEON-DS-Airborne-Remote-Sensing/HARV/RGB_Imagery/HARV_Ortho_wNA.tif")`    

 Plot the object as a true color image: `plotRGB(HARV_NA, r= 1, g = 2, b = 3)`    

4. What happened to the black edges in the data?
5. What does this tell us about the difference in the data structure between `HARV_Ortho_wNA.tif` and `HARV_RGB_Ortho.tif` (R object `RGB_stack`). How can you check?

<details>
<summary>Solution
</summary>

1.  Yes
2.  -9999
2. 3
6. 
