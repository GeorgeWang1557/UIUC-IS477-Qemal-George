**Course Project IS77: Climate and Earthquake Data Integration Analysis**

Contributors
- George Wang
- Qemal Ramadini


**Summary**
This project investigates whether the frequency of earthquake activities has a relationship with regional temperature. Understanding potential interactions between climate conditions 
and seismic activity is an emerging interdisciplinary topic with implications for both climate scientists and tectonic researchers. While the current scientific consensus holds that 
temperature generally influences the atmosphere more than the lithosphere, some researchers have explored whether long-term climate variations, melting of glaciers, or changes in land 
mass distribution can influence stress conditions along tectonic plates. Our project does not attempt to prove causation, but rather to explore whether measurable statistical associations 
exist between regional temperature and earthquake frequency using openly available global datasets.

To address this question, we integrated two large-scale datasets from Kaggle: the Climate Change: Earth Surface Temperature Data (Berkeley Earth) and the Global Earthquake–Tsunami Risk 
Assessment Dataset (Ahmed Uzaki). These datasets span multiple decades and include variables such as temperature, latitude and longitude positions, earthquake magnitude, depth, and tsunami 
indicators. The analysis required substantial data preparation, including harmonizing coordinate formats, filtering time periods, verifying dataset integrity using SHA-256 hashing, and 
constructing regional and yearly summary tables.

Our workflow consisted of several stages. First, we loaded and cleaned the climate dataset to retain only years from 2000 onward and standardized the temperature records across regions. 
The earthquake dataset required conversion of directional latitude and longitude strings (for example, "45N", "120W") into signed numerical coordinates. Both datasets were cleaned in 
Python and partially validated through OpenRefine. After cleaning, we aggregated each dataset at two levels: global yearly summaries, and regional summaries using 10-degree 
latitude–longitude bins. These summaries allowed us to compare climate trends and seismic trends at matching spatial and temporal scales.

We then merged the climate and earthquake summaries by region and year. This matching process enabled correlation computations between average regional temperature and metrics such as 
earthquake count, magnitude, depth, and tsunami frequency. Aggregating by region allowed us to capture more nuanced signals that may not appear in global averages, since temperature or 
seismicity patterns may be heavily localized.

Finally, we conducted descriptive analysis, correlation testing, visualizations, and SQL queries using DuckDB. Our findings suggest that, based on the data processed, no strong or 
consistent correlations exist between regional temperature and earthquake frequency. Some regions displayed weak positive or negative correlations, but these varied significantly and 
were not statistically meaningful. As a result, the project supports the interpretation that seismic activity is overwhelmingly driven by geological processes rather than atmospheric or 
temperature-driven dynamics.


**Data Profile**
Climate Dataset (Berkeley Earth / Self-Compiled Source)
The climate dataset used in this project originates from Berkeley Earth but is distributed through a simplified version on Kaggle. Importantly, Kaggle is not the primary source of this 
dataset. Rather, the Kaggle uploader repackaged climate data that Berkeley Earth originally compiled from a wide network of temperature monitoring stations around the world. Berkeley Earth 
aggregates measurements from national meteorological organizations, independent monitoring stations, and historical climate logs, merging them into a unified dataset through a proprietary 
quality-control and homogenization pipeline. Unlike many datasets, this particular upload does not provide citations listing the original contributing institutions or agencies. As a result, 
while the Kaggle page claims to represent Berkeley Earth data, it offers limited transparency about the underlying sampling methodology, provenance, or update history. This lack of 
documentation introduces some uncertainty regarding how temperatures were aggregated, corrected, or interpolated before reaching the Kaggle version.

The dataset includes monthly average temperatures, temperature uncertainties, and spatial coordinates for thousands of global locations. Because the dataset was not accompanied by a 
metadata appendix explaining the collection protocol, limitations such as missing records, inconsistent monitoring frequency, or methodological changes over time cannot be fully evaluated. 
Nonetheless, the dataset is widely used in climate research because Berkeley Earth is considered a reputable and scientifically rigorous organization. For this project, the dataset was 
filtered to only include records from the year 2000 onward so that it aligns with the temporal coverage of the earthquake dataset. The climate dataset is distributed under CC BY 4.0, 
allowing academic researchers to freely analyze, repurpose, and share the material as long as attribution is provided.

Earthquake Dataset (EveryEarthquake API → Kaggle Repackage)
The earthquake dataset used in this project originates from the EveryEarthquake API, a structured data service available through RapidAPI. The Kaggle version of the dataset is not the 
authoritative source; instead, it represents a static snapshot compiled and uploaded by a Kaggle user. The primary source, the EveryEarthquake API, aggregates seismic records from multiple 
international geophysical monitoring agencies, including the United States Geological Survey (USGS) and other global seismic networks. The API provides detailed parameters for each 
earthquake event, including timestamp, magnitude, depth, latitude, longitude, and a tsunami indicator flag. The Kaggle uploader collected a large subset of these API outputs and packaged 
them into a single CSV file.

Because Kaggle hosts a static snapshot rather than a live connection to the API, the dataset loses some of the dynamic documentation that typically accompanies scientific APIs. There is 
limited information in the Kaggle version regarding when the snapshot was extracted, whether any transformations were applied, or how missing or anomalous values were handled before 
publication. The coordinate system is stored using directional notation, which required additional processing before the dataset could be used for spatial analysis. Despite this, the 
dataset retained enough structure and granularity for regional aggregation and correlation testing.


**Data Quality**
Several data quality challenges emerged during the preparation and integration of these datasets, each requiring targeted cleaning procedures to ensure analytical validity. One major issue 
involved the inconsistent coordinate formats in the earthquake dataset. Instead of using numeric latitude and longitude values, the dataset stored coordinates with directional suffixes such 
as N, S, E, and W. This prevented direct grouping and spatial binning until we developed a conversion function that transformed directional strings into signed floats. After applying this 
transformation, we examined several converted entries manually to ensure accuracy. This step was necessary because improperly converted coordinates could misplace entire regions.

Missing values presented another concern. In the climate dataset, some records lacked temperature uncertainty fields or contained incomplete location descriptors. In the earthquake dataset,
missing values occurred in magnitude and depth fields. Because missing values could distort averages and correlations, incomplete records were removed. While this reduces total sample size,
it improves the integrity of derived statistics.

Temporal alignment was another essential part of quality assurance. The climate dataset spans several centuries, whereas the earthquake dataset mostly covers recent decades. To ensure 
comparability, climate observations were limited to the years 2000 through 2013, matching the earthquake dataset's temporal window. Pandas' datetime functions were used to parse and 
standardize date fields.

Dataset integrity was also verified using SHA-256 hashing. Hashing allows future researchers to confirm that they are working with exactly the same dataset version. Computing hash values 
ensured that our cleaned datasets were stable and unchanged before analysis.

Finally, spatial binning into 10-degree regional grids introduced trade-offs between precision and comparability. While binning is necessary for aligning datasets with different spatial 
resolutions, it also reduces geographic detail. To avoid unreliable correlations, regions with fewer than six valid observations were excluded from final analysis.


**Findings**
The primary research question examined whether regional temperature correlates with earthquake frequency. After aggregating data into 10-degree latitude–longitude bins and computing 
yearly summaries, correlation tests were performed between average regional temperatures and earthquake metrics including count, magnitude, and depth.

Results consistently showed no meaningful relationship between temperature and earthquake activity. Most correlation coefficients were near zero. In rare cases, weak positive correlations
emerged, indicating that higher temperatures coincided with slightly increased earthquake counts, but these patterns were inconsistent across regions and lacked theoretical grounding. 
Weak negative correlations also appeared in some regions, but these too varied unpredictably and likely resulted from noise rather than meaningful interaction.

Magnitude and depth showed no relationship to temperature. Since earthquake severity is governed by tectonic stress release rather than atmospheric temperature, this outcome aligns 
with geological theory. Regions with high seismicity, such as the Pacific Ring of Fire, demonstrated no association with temperature trends.

Overall, the findings confirm that seismic behavior is overwhelmingly driven by tectonic processes rather than climatic ones. Within the scope of available datasets, there is no 
evidence supporting a temperature–earthquake relationship.


**Future Work**
While this project successfully demonstrates an end-to-end data workflow involving acquisition, cleaning, integration, profiling, and exploratory analysis of global earthquake activity 
and long-term temperature trends, several lessons emerged that point to meaningful extensions for future work. These opportunities span methodological improvements, enriched data sources, 
expanded statistical modeling, enhanced geospatial analysis, and more sophisticated workflow automation. Together, they offer a pathway toward transforming the current project from a 
foundational exploratory study into a more rigorous scientific investigation.

One of the most important lessons learned was the complexity of integrating datasets that differ in structure, granularity, and geographic precision. Earthquake data is recorded at the 
coordinate level, while climate data is often aggregated by city or country. This mismatch required careful cleaning, geographic normalization, and the creation of composite keys like 
combined latitude-longitude strings. Although these steps allowed the datasets to be merged for exploratory comparisons, future work should implement more robust geospatial methods. 
For example, reverse-geocoding the earthquake coordinates to the nearest administrative boundary would allow for cleaner joins with climate datasets aggregated by region. Tools such as 
GeoPandas, Shapely, and external boundary shapefiles could support this transformation. This improvement would significantly enhance the accuracy of cross-dataset comparisons and allow 
future analyses to examine patterns not only globally but within specific countries or tectonic zones.

Another major lesson is the importance of increasing the temporal resolution of the analysis. While this project focused on annual or monthly aggregates to identify broad patterns, 
seismic activity is inherently high-frequency data. A more refined workflow could analyze daily or hourly sequences, enabling event-based matching between earthquakes and environmental 
anomalies. Linking rapid temperature fluctuations, oceanic pressure changes, or seasonal climate cycles with seismic clusters would require additional datasets such as sea-surface 
temperature (SST) records, ENSO indices, or atmospheric models but would open the door to deeper scientific inquiry. Incorporating such datasets would also offer opportunities to perform 
Granger causality testing or time-lagged correlation analysis, which could reveal whether certain environmental conditions consistently precede changes in seismic behavior.

From a statistical perspective, future work could develop more formal models of the relationship between temperature trends and earthquake characteristics. This project identified 
descriptive patterns such as whether earthquake frequency rises in years with higher global temperatures but did not yet attempt predictive modeling. A next step could be building 
regression models, random forests, or multivariate time-series models to explore whether climate indicators can meaningfully predict seismic metrics like magnitude or depth at the 
regional level. While causation is unlikely given the underlying tectonic physics, modeling could reveal whether environmental variables serve as weak but consistent signals for broader 
seismic cycles. If combined with tectonic boundary data or fault-line proximity characteristics, these models could become substantially more powerful.

There is also an opportunity to enhance the workflow automation and reproducibility components of the project. Although the current pipeline includes data acquisition through KaggleHub, 
cleaning scripts, and integration workflows, transforming the end-to-end process into a fully automated Snakemake or Makefile-based pipeline would greatly improve reproducibility. 
Future work could incorporate containerization tools such as Docker to standardize the runtime environment across machines. This would ensure that researchers and graders can reproduce 
analysis steps without dependency conflicts, version mismatches, or differing Python environments. Adding unit tests for each transformation step would further increase confidence in the
correctness of the results.

Visualization is another area for expansion. While initial plots demonstrate broad trends, future work could incorporate interactive dashboards created with Plotly Dash, Streamlit, or 
Tableau Public. These dashboards could allow users to filter earthquakes by region, magnitude, depth, and time while simultaneously viewing corresponding temperature trends. Spatial 
visualizations could also be enhanced by integrating global tectonic boundary maps, volcano locations, or seismic intensity contours. A dynamic geospatial dashboard showing earthquakes 
overlaid on warming or cooling regions could provide a compelling way to communicate complex relationships to the public or to policymakers.

Finally, a major future direction is expanding the scope beyond earthquakes to study a larger system of Earth processes. For example, datasets on volcanic eruptions, ocean acidity, 
glacier melt, or sea-level rise could provide additional insight into how geological and environmental systems interact. Integrating these sources would allow researchers to examine 
whether warming trends affect not just seismic activity but other stress indicators across the Earth’s crust and oceans. The project could gradually evolve into a broader Earth-systems 
analysis platform combining geophysics, climate science, and environmental monitoring.

Overall, the project successfully demonstrated how two large and heterogeneous datasets can be integrated to explore cross-domain scientific questions. At the same time, it revealed 
numerous opportunities for deeper analysis, improved methods, and expanded scope. The lessons learned during this process provide a roadmap for advancing the project in ways that increase 
both scientific rigor and practical applicability. With additional datasets, more advanced modeling, and stronger geospatial tools, future iterations of this project could yield 
meaningful insights into how Earth’s physical and environmental systems evolve together over time.


**Reproducing the Results**
Please see separate document for reproduction process and code file guideline.
