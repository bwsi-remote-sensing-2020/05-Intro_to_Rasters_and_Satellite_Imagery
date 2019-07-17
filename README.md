# Satellite_Image_Processing_Using_Python
We will be using the raster.io and GDAL python libraries to acquire landsat_8 satellite images from AWS and process them. 

# Background
GDAL is a powerful and mature library for reading, writing and warping raster datasets, written in C++ with bindings to other languages. There are a variety of geospatial libraries available on the python package index, and almost all of them depend on GDAL. One such python library developed and supported by Mapbox, rasterio, builds on top of GDAL’s many features, but provides a more pythonic interface and supports many of the features and formats that GDAL supports. Both GDAL and rasterio are constantly being updated and improved: As of writing this tutorial (July 2019), GDAL is at version 3.0.1 and rasterio is at version 1.0.24.

When should you use GDAL directly?

If you are comfortable with the terminal, GDAL’s command line utilities are very useful for scripting.
Note that GDAL also has auto-generated Python bindings.
When should you use rasterio instead of GDAL?

If you are working in a Python environment (ipython, scripts, jupyter lab)
When might these not be the best tools?

Both libraries are critical for input/output operations, but you’ll draw on other libraries for computation (e.g. numpy)

That said, GDAL does have some standard processing scripts (for example pan-sharpening) and rasterio provides a plugin interface for workflows

For polished map creation and interactive visualization, a desktop GIS software like QGIS may be a better, more fully-featured choice.

# Loading and viewing Landsat 8 imagery
The Landsat program is the longest-running civilian satellite imagery program, with the first satellite launched in 1972 by NASA and the US Geological Survey. Landsat 8 is the latest satellite in this program, and was launched in 2013. Landsat observations are processed into “scenes”, each of which is approximately 183 km x 170 km, with a spatial resolution of 30 meters and a temporal resolution of 16 days. The duration of the landsat program makes it an attractive source of medium-scale imagery for land surface change analyses. Landsat is multiband imagery, you can read more about it at https://www.usgs.gov/land-resources/nli/landsat.

One reason we’ll use Landsat 8 for this demo is that the entire Landsat 8 archive is hosted by various commercial Cloud providers with free public access (AWS and Google Cloud)!

# Satellite archives on the Cloud
A single Landsat8 scene is about 1 Gb in size since it contains a large array of data for each imagery band. Images are often grouped into a single .tar.gz file, which requires scientists to download the entire 1 Gb, even if you are just interested in a single band or subset of the image. To circumvent downloading, a new format has been proposed for Cloud storage called Cloud-optimized Geotiffs (COGs), to allow easy access to individual bands or even subsets of single images.

In a strict sense, the Landsat images on AWS and Google Cloud are not COGs (you can validate the format with online tools such as this - http://cog-validate.radiant.earth/html). Nevertheless, the files are stored as tiled Geotiffs, so many COG features still work.

# AWS versus Google Cloud
You can use NASA’s Earthdata Search website to discover data. Landsat images are organized by ‘path’ and ‘row’. We’ve chosen a scenes from path 42, row 34, that doesn’t have many clouds present (LC08_L1TP_042034_20170616_20170629_01_T1). Note that ‘T1’ stands for ‘Tier 1’ (for analytic use), and ‘RT’ stands for ‘Real-time’, for which quality control is not as rigorous. Read more about the various Landsat formats and collections here. ​ At first glance it seems that you can find this data on both AWS or Google Cloud, for example look at the band 4 image for the date we selected: ​

http://landsat-pds.s3.amazonaws.com/c1/L8/042/034/LC08_L1TP_042034_20170616_20170629_01_T1/LC08_L1TP_042034_20170616_20170629_01_T1_B4.TIF ​ ​

Or, the same file is available on Google Cloud storage: ​

https://storage.googleapis.com/gcp-public-data-landsat/LC08/01/042/034/LC08_L1TP_042034_20170616_20170629_01_T1/LC08_L1TP_042034_20170616_20170629_01_T1_B4.TIF
