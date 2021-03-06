# Tutorial: How to run the NPS night-time radiance calculation

`Clip_raster_and_calculate_ALR_Python.py` takes in a compressed tgz of night-time radiance composites from the VIIRS DNB, processed by the Earth Observations Group. You can download the composites by month and by location [here](https://eogdata.mines.edu/download_dnb_composites.html).

The script reads the compressed file into memory, clips to a buffered polygon specified by the user and reprojects to an equal-area Albers projection. It then uses a neighborhood annulus method to calculate the effect of night-time light at varying distances from a source. 

Finally, it reclips the result to the region of interest and saves the output to a destination folder.

Because the algorithm calculates the effects of light up to 300 km away from an area of interest, a buffer of 300 km around the user-provided polygon will be generated and used to calculate ALR; the output will later be reclipped to the original polygon. Keep this in mind when selecting tile images from the VIIRS DNB. 

<p align="center">
<img src="/images/buffer_example_crop.jpg" height="400" width="400">
    <br/> An example of how the satellite data is buffered and cropped.
</p>

## Before running the code

1. Download the nighttime radiance composites from the VIIRS DNB, available [here](https://eogdata.mines.edu/download_dnb_composites.html). Pick the tile that corresponds to your region. 
2. Make sure you have a polygon that represents the region you're interested in. 
3. Install the necessary dependencies (see below).

## Set-up

Start by opening `Clip_raster_and_calculate_ALR_Python.py` and navigate to the `## USER INPUTS` section, line 24. You'll want to change all of these variables to match your set-up. <br/>
* `working_dir` is where you want to store all your outputs. <br/>
* `geodb` is the location of a File GDB you're using to store the feature class representing the region you're interested in. If you're using a shapefile, set this equal to False. <br/>
* `roi` is the region of interest, either the name of a feature class or path to a shapefile. <br/>
* `name` is used for labeling raster output. <br/>
* `out_proj` should be an equal-area Albers projection, to ensure accuracy with the convolution process. Look up the EPSG code that corresponds to the correct projection for your region. <br/>
* `image_tile` is the tile value selected from the VIIRS/DNB composites. <br/>
* `verbosity` gives you the option to log output as the program runs.

You should not need to change any other inputs!

## Dependencies:
* `NumPy`
* `Astropy`
* `Geopandas`
* `Gdal`

You can run the code via:

`python Clip_raster_and_calculate_ALR_Python.py [path_to_DNB_tgz_file.tgz]`

The outputs will be saved to a folder called `ALR_outputs`.

To calculate ALR for multiple months, you can write a batch script such as: 
```REM U:
REM cd ..\Documents\Code

for %%I in (..\..\Data\RasterData\DNB_VIIRS_EOG\May_Nov_data\*.tgz) do (echo %%I & python Code_to_calculate_ALR\Clip_raster_and_calculate_ALR.py %%~fI)
```

## Other
* `CreateMosaicDataset.py` is a quick and dirty way to create a mosaic dataset of your ALR raster outputs, which can then be manipulated to display changes in ALR over time.
* `Reclassify_ALR_rasters.py` is similarly a quick way to produce rasters classified by night-sky quality classes, i.e. good, moderate, poor and Milky Way invisible, per Duriscoe et al.
* `ALR_raster_to_time-aware_polygon.py` generates a polygon feature class from the ALR raster outputs, calculates area and percent cover, and adds a date field that can be used to publish time-aware data on ArcGIS Online.
