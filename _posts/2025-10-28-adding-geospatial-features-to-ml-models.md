---
layout: single
title: "Adding Geospatial Features to Your Machine Learning Models"
date: 2025-09-28 22:00:00 +0700
comments: true
tags: [Information Technology, GeoAI]
---

## Introduction

When we build machine learning models, we often focus on features like numbers, categories, and texts. But there’s one feature type that often gets ignored: location.

And that’s a problem, because location is more than just a pair of coordinates - it carries context. For example:

- Two houses with the same number of rooms can have very different prices depending on how close they are to the city center.
- A restaurant’s success depends not only on its menu, but also on the number of offices or schools around it.
- Crop yield is influenced not just by fertilizer, but by soil type, elevation, and distance to rivers.

In other words, location shapes outcomes. The good news is: adding location is easier than you think.

In this hands-on tutorial, we’ll walk through building a location-aware machine learning model using Airbnb data from New York City. This model can predict the price of an Airbnb listing.

## Setting up the Environment

Before working with the dataset, it’s important to set up the right environment. In this project, we’ll combine Python’s data science and geospatial analysis ecosystem. The toolkit includes core libraries such as pandas for data handling, geopandas (an extension of pandas for spatial data), shapely for geometric operations, and scikit-learn for machine learning. For mapping and data access, we’ll rely on folium to create interactive maps and osmnx to seamlessly download data from OpenStreetMap.


```python
# !pip install pandas geopandas shapely scikit-learn folium osmnx
```


```python
import pandas as pd
import geopandas as gpd
import shapely
import sklearn
import folium
import osmnx as ox

print("All libraries loaded successfully!")
```

	All libraries loaded successfully!


If that runs without errors, you’re ready to go.

## Dataset Preparation

Now that our environment is ready, let’s load some real-world data. For this tutorial, we’ll use the Inside Airbnb dataset for New York City. This dataset is public and widely used by researchers, making it a great starting point.

### About the dataset

The Airbnb NYC dataset includes:

- Basic info about listings (price, room_type, number_of_reviews, availability…).
- Location info (latitude and longitude).
- Neighborhood details.

### Download dataset

You can grab the dataset directly from Inside Airbnb (https://insideairbnb.com/get-the-data/) and look for: New York City, Detailed Listing data (CSV).

### Load data in Python


```python
import pandas as pd

df = pd.read_csv("./data/listings.csv")

df.head(3)
```




<div>
<style scoped>
	.dataframe tbody tr th:only-of-type {
		vertical-align: middle;
	}

	.dataframe tbody tr th {
		vertical-align: top;
	}

	.dataframe thead th {
		text-align: right;
	}
</style>
<table border="1" class="dataframe">
  <thead>
	<tr style="text-align: right;">
	  <th></th>
	  <th>id</th>
	  <th>listing_url</th>
	  <th>scrape_id</th>
	  <th>last_scraped</th>
	  <th>source</th>
	  <th>name</th>
	  <th>description</th>
	  <th>neighborhood_overview</th>
	  <th>picture_url</th>
	  <th>host_id</th>
	  <th>...</th>
	  <th>review_scores_communication</th>
	  <th>review_scores_location</th>
	  <th>review_scores_value</th>
	  <th>license</th>
	  <th>instant_bookable</th>
	  <th>calculated_host_listings_count</th>
	  <th>calculated_host_listings_count_entire_homes</th>
	  <th>calculated_host_listings_count_private_rooms</th>
	  <th>calculated_host_listings_count_shared_rooms</th>
	  <th>reviews_per_month</th>
	</tr>
  </thead>
  <tbody>
	<tr>
	  <th>0</th>
	  <td>2539</td>
	  <td>https://www.airbnb.com/rooms/2539</td>
	  <td>20250801203054</td>
	  <td>2025-08-03</td>
	  <td>city scrape</td>
	  <td>Superfast Wi-Fi.  Clean &amp; quiet home by the park</td>
	  <td>Bright, serene room in a renovated apartment h...</td>
	  <td>Close to Prospect Park and Historic Ditmas Park</td>
	  <td>https://a0.muscache.com/pictures/hosting/Hosti...</td>
	  <td>2787</td>
	  <td>...</td>
	  <td>5.0</td>
	  <td>4.75</td>
	  <td>4.88</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>6</td>
	  <td>1</td>
	  <td>5</td>
	  <td>0</td>
	  <td>0.08</td>
	</tr>
	<tr>
	  <th>1</th>
	  <td>2595</td>
	  <td>https://www.airbnb.com/rooms/2595</td>
	  <td>20250801203054</td>
	  <td>2025-08-02</td>
	  <td>city scrape</td>
	  <td>Skylit Studio Oasis | Midtown Manhattan</td>
	  <td>Prime Midtown | Spacious 500 Sq Ft | Pyramid S...</td>
	  <td>Centrally located in the heart of Manhattan ju...</td>
	  <td>https://a0.muscache.com/pictures/hosting/Hosti...</td>
	  <td>2845</td>
	  <td>...</td>
	  <td>4.8</td>
	  <td>4.81</td>
	  <td>4.40</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>3</td>
	  <td>3</td>
	  <td>0</td>
	  <td>0</td>
	  <td>0.25</td>
	</tr>
	<tr>
	  <th>2</th>
	  <td>6848</td>
	  <td>https://www.airbnb.com/rooms/6848</td>
	  <td>20250801203054</td>
	  <td>2025-08-03</td>
	  <td>city scrape</td>
	  <td>Only 2 stops to Manhattan studio</td>
	  <td>Comfortable studio apartment with super comfor...</td>
	  <td>NaN</td>
	  <td>https://a0.muscache.com/pictures/e4f031a7-f146...</td>
	  <td>15991</td>
	  <td>...</td>
	  <td>4.8</td>
	  <td>4.69</td>
	  <td>4.58</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>1</td>
	  <td>1</td>
	  <td>0</td>
	  <td>0</td>
	  <td>0.99</td>
	</tr>
  </tbody>
</table>
<p>3 rows × 79 columns</p>
</div>



### Turn it into a GeoDataFrame

Since we’ll be working with spatial features, let’s convert it into a GeoDataFrame using geopandas:


```python
import geopandas as gpd
from shapely.geometry import Point

gdf = gpd.GeoDataFrame(
	df,
	geometry=gpd.points_from_xy(df.longitude, df.latitude),
	crs="EPSG:4326"
)

gdf.head(3)
```




<div>
<style scoped>
	.dataframe tbody tr th:only-of-type {
		vertical-align: middle;
	}

	.dataframe tbody tr th {
		vertical-align: top;
	}

	.dataframe thead th {
		text-align: right;
	}
</style>
<table border="1" class="dataframe">
  <thead>
	<tr style="text-align: right;">
	  <th></th>
	  <th>id</th>
	  <th>listing_url</th>
	  <th>scrape_id</th>
	  <th>last_scraped</th>
	  <th>source</th>
	  <th>name</th>
	  <th>description</th>
	  <th>neighborhood_overview</th>
	  <th>picture_url</th>
	  <th>host_id</th>
	  <th>...</th>
	  <th>review_scores_location</th>
	  <th>review_scores_value</th>
	  <th>license</th>
	  <th>instant_bookable</th>
	  <th>calculated_host_listings_count</th>
	  <th>calculated_host_listings_count_entire_homes</th>
	  <th>calculated_host_listings_count_private_rooms</th>
	  <th>calculated_host_listings_count_shared_rooms</th>
	  <th>reviews_per_month</th>
	  <th>geometry</th>
	</tr>
  </thead>
  <tbody>
	<tr>
	  <th>0</th>
	  <td>2539</td>
	  <td>https://www.airbnb.com/rooms/2539</td>
	  <td>20250801203054</td>
	  <td>2025-08-03</td>
	  <td>city scrape</td>
	  <td>Superfast Wi-Fi.  Clean &amp; quiet home by the park</td>
	  <td>Bright, serene room in a renovated apartment h...</td>
	  <td>Close to Prospect Park and Historic Ditmas Park</td>
	  <td>https://a0.muscache.com/pictures/hosting/Hosti...</td>
	  <td>2787</td>
	  <td>...</td>
	  <td>4.75</td>
	  <td>4.88</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>6</td>
	  <td>1</td>
	  <td>5</td>
	  <td>0</td>
	  <td>0.08</td>
	  <td>POINT (-73.97238 40.64529)</td>
	</tr>
	<tr>
	  <th>1</th>
	  <td>2595</td>
	  <td>https://www.airbnb.com/rooms/2595</td>
	  <td>20250801203054</td>
	  <td>2025-08-02</td>
	  <td>city scrape</td>
	  <td>Skylit Studio Oasis | Midtown Manhattan</td>
	  <td>Prime Midtown | Spacious 500 Sq Ft | Pyramid S...</td>
	  <td>Centrally located in the heart of Manhattan ju...</td>
	  <td>https://a0.muscache.com/pictures/hosting/Hosti...</td>
	  <td>2845</td>
	  <td>...</td>
	  <td>4.81</td>
	  <td>4.40</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>3</td>
	  <td>3</td>
	  <td>0</td>
	  <td>0</td>
	  <td>0.25</td>
	  <td>POINT (-73.98559 40.75356)</td>
	</tr>
	<tr>
	  <th>2</th>
	  <td>6848</td>
	  <td>https://www.airbnb.com/rooms/6848</td>
	  <td>20250801203054</td>
	  <td>2025-08-03</td>
	  <td>city scrape</td>
	  <td>Only 2 stops to Manhattan studio</td>
	  <td>Comfortable studio apartment with super comfor...</td>
	  <td>NaN</td>
	  <td>https://a0.muscache.com/pictures/e4f031a7-f146...</td>
	  <td>15991</td>
	  <td>...</td>
	  <td>4.69</td>
	  <td>4.58</td>
	  <td>NaN</td>
	  <td>f</td>
	  <td>1</td>
	  <td>1</td>
	  <td>0</td>
	  <td>0</td>
	  <td>0.99</td>
	  <td>POINT (-73.95342 40.70935)</td>
	</tr>
  </tbody>
</table>
<p>3 rows × 80 columns</p>
</div>



### Quick Visualization

Let’s quickly check where the listings are located on a map:


```python
import folium

# Pick a center (Manhattan coordinates)
m = folium.Map(location=[40.75, -73.98], zoom_start=11)

for idx, row in gdf.head(500).iterrows():
	folium.CircleMarker([row.latitude, row.longitude], radius=2, color="red", fill=True).add_to(m)

m
```



![png](/images/blogs/adding_geospatial_features_to_ml_model/NYC_map.png)


You should see an interactive map with points spread across New York’s boroughs.

## Feature Engineering with Location

Although our dataset knows where each Airbnb is located, raw latitude/longitude numbers don't mean much for a machine learning model. We need to transform location into features that capture geographic context. Here are four useful and simple types of location-based features we'll create.

### 1. Distance to the City Center

Why it matters: in most cities, listings closer to the city center tend to be more expensive. Proximity to central business districts often reflects accessibility, demand, and prestige.


```python
from shapely.geometry import Point

# Define the city center (Times Square)
city_center = Point(-73.9855, 40.7580)

# Computer distance
gdf = gdf.to_crs(epsg=3857) # Project to metric system
gdf['dist_city_center'] = gdf.geometry.distance(city_center)
gdf[['neighbourhood_group_cleansed', 'price', 'dist_city_center']].head(3)
```




<div>
<style scoped>
	.dataframe tbody tr th:only-of-type {
		vertical-align: middle;
	}

	.dataframe tbody tr th {
		vertical-align: top;
	}

	.dataframe thead th {
		text-align: right;
	}
</style>
<table border="1" class="dataframe">
  <thead>
	<tr style="text-align: right;">
	  <th></th>
	  <th>neighbourhood_group_cleansed</th>
	  <th>price</th>
	  <th>dist_city_center</th>
	</tr>
  </thead>
  <tbody>
	<tr>
	  <th>0</th>
	  <td>Brooklyn</td>
	  <td>$260.00</td>
	  <td>9.612996e+06</td>
	</tr>
	<tr>
	  <th>1</th>
	  <td>Manhattan</td>
	  <td>$240.00</td>
	  <td>9.622467e+06</td>
	</tr>
	<tr>
	  <th>2</th>
	  <td>Brooklyn</td>
	  <td>$98.00</td>
	  <td>9.616044e+06</td>
	</tr>
  </tbody>
</table>
</div>



### 2. Distance to the Nearest Park

People love staying near green spaces - parks improve livability, reduce noise, and offer recreation.


```python
import osmnx as ox

# Get all paarks in NYC from OpenStreetMap
tags = {'leisure': 'park'}
parks = ox.features_from_place("New York City, USA", tags)

# Convert to centroids
parks = parks.to_crs(epsg=3857).centroid

# Computer nearest park distance
gdf['dist_nearest_park'] = gdf.geometry.apply(lambda x: parks.distance(x).min())
```

### 3. Distance to the Nearest Subway Station

In New York, proximity to the subway is a huge factor. It can make or break the attractiveness of a listing. This is where location truly shines - coordinates alone don't tell you if there's a subway nearby.



```python
tags = {"railway": "station", "station": "subway"}
subways = ox.features_from_place("New York City, USA", tags)

subways = subways.to_crs(epsg=3857).centroid

gdf['dist_nearest_subway'] = gdf.geometry.apply(lambda x: subways.distance(x).min())

gdf[['neighbourhood_group_cleansed', 'price', 'dist_city_center', 'dist_nearest_park', 'dist_nearest_subway']].head(3)
```




<div>
<style scoped>
	.dataframe tbody tr th:only-of-type {
		vertical-align: middle;
	}

	.dataframe tbody tr th {
		vertical-align: top;
	}

	.dataframe thead th {
		text-align: right;
	}
</style>
<table border="1" class="dataframe">
  <thead>
	<tr style="text-align: right;">
	  <th></th>
	  <th>neighbourhood_group_cleansed</th>
	  <th>price</th>
	  <th>dist_city_center</th>
	  <th>dist_nearest_park</th>
	  <th>dist_nearest_subway</th>
	</tr>
  </thead>
  <tbody>
	<tr>
	  <th>0</th>
	  <td>Brooklyn</td>
	  <td>$260.00</td>
	  <td>9.612996e+06</td>
	  <td>695.029979</td>
	  <td>832.818934</td>
	</tr>
	<tr>
	  <th>1</th>
	  <td>Manhattan</td>
	  <td>$240.00</td>
	  <td>9.622467e+06</td>
	  <td>190.688019</td>
	  <td>149.614085</td>
	</tr>
	<tr>
	  <th>2</th>
	  <td>Brooklyn</td>
	  <td>$98.00</td>
	  <td>9.616044e+06</td>
	  <td>272.811822</td>
	  <td>366.446845</td>
	</tr>
  </tbody>
</table>
</div>



### 4. Neighborhood Density

Density captures how "crowded" an area is. A high density might signal popularity (lots of demand) but also strong competition.


```python
from sklearn.neighbors import BallTree
import numpy as np

# Extract coordinates in radians
coords = np.vstack([gdf.geometry.y, gdf.geometry.x]).T
coords_rad = np.radians(coords)

tree = BallTree(coords_rad, metric="haversine")

# Count neighbors within 1 km
neighbors_count = tree.query_radius(coords_rad, r=1000/6371000, count_only=True)
gdf['density_1km'] = neighbors_count

gdf[['neighbourhood_group_cleansed', 'price', 'dist_city_center', 'dist_nearest_park', 'density_1km']].head(3)
```




<div>
<style scoped>
	.dataframe tbody tr th:only-of-type {
		vertical-align: middle;
	}

	.dataframe tbody tr th {
		vertical-align: top;
	}

	.dataframe thead th {
		text-align: right;
	}
</style>
<table border="1" class="dataframe">
  <thead>
	<tr style="text-align: right;">
	  <th></th>
	  <th>neighbourhood_group_cleansed</th>
	  <th>price</th>
	  <th>dist_city_center</th>
	  <th>dist_nearest_park</th>
	  <th>density_1km</th>
	</tr>
  </thead>
  <tbody>
	<tr>
	  <th>0</th>
	  <td>Brooklyn</td>
	  <td>$260.00</td>
	  <td>9.612996e+06</td>
	  <td>695.029979</td>
	  <td>1</td>
	</tr>
	<tr>
	  <th>1</th>
	  <td>Manhattan</td>
	  <td>$240.00</td>
	  <td>9.622467e+06</td>
	  <td>190.688019</td>
	  <td>1</td>
	</tr>
	<tr>
	  <th>2</th>
	  <td>Brooklyn</td>
	  <td>$98.00</td>
	  <td>9.616044e+06</td>
	  <td>272.811822</td>
	  <td>1</td>
	</tr>
  </tbody>
</table>
</div>



### Our Location Features

At this point, our dataset has four new features:
- dist_city_center → Accessibility to Manhattan’s core
- dist_nearest_park → Environmental quality
- density_1km → Neighborhood competition/attractiveness
- dist_nearest_subway → Transportation accessibility

Together, these features show why location is more than just lat/long. They turn spatial context into measurable signals that our model can actually use.

✅ With this enriched dataset, we’re ready for the fun part: training two models — one without location features, and one with them — and seeing how much location improves predictions.

## Training the Model

### 📊 Baseline model (without location)

For the baseline, we'll use only non-spatial attributes:
- room_type
- minimum_nights
- number_of_reviews
- availability_365

We will predict the price of a listing.


```python
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, r2_score

# Select baseline features
baseline_features = ["room_type", "minimum_nights", "number_of_reviews", "availability_365"]

gdf['price'] = gdf['price'].astype(str).str.replace("$", "", regex=False)
gdf['price'] = gdf['price'].astype(str).str.replace(",", "", regex=False)
gdf['price'] = gdf['price'].astype(float)
gdf = gdf[gdf['price'].notna()]

# Encode categorical variable (room_type)
df_encoded = pd.get_dummies(gdf[baseline_features + ["price"]], drop_first=True)
X = df_encoded.drop("price", axis=1)
y = df_encoded["price"]

# Train/test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train baseline model
baseline_model = RandomForestRegressor(n_estimators=100, random_state=42)
baseline_model.fit(X_train, y_train)

# Predictions
y_pred_baseline = baseline_model.predict(X_test)

# Evaluate
print("Baseline MAE: ", mean_absolute_error(y_test, y_pred_baseline))
```

	Baseline MAE:  219.09178855984604


### 🌍 Location-Aware Model (With spatial features)
Now let's add our geographic features:
- dist_city_center
- dist_nearest_park
- dist_nearest_subway
- density_1km


```python
# Select both baseline + spatial features
spatial_features = baseline_features + ["dist_city_center", "dist_nearest_park", "dist_nearest_subway", "density_1km"]

df_encoded_spatial = pd.get_dummies(gdf[spatial_features + ["price"]], drop_first=True)

X2 = df_encoded_spatial.drop("price", axis=1)
y2 = df_encoded_spatial["price"]

# Train/test split
X2_train, X2_test, y2_train, y2_test = train_test_split(X2, y2, test_size=0.2, random_state=42)

# train location-aware model
spatial_model = RandomForestRegressor(n_estimators=100, random_state=42)
spatial_model.fit(X2_train, y2_train)

# Prediction
y_pred_spatial = spatial_model.predict(X2_test)

# Evaluate
print("Location-Aware MAE: ", mean_absolute_error(y2_test, y_pred_spatial))
```

	Location-Aware MAE:  205.12980100334863


At this point, the location-aware model outperforms the baseline. The MAE of the location-aware model is lower than the baseline -> the predictions are closer to the real price than the prediction without using spatial features.

## Evaluating and Visualizing Results

### Feature Importance


```python
import matplotlib.pyplot as plt
import seaborn as sns

importances = spatial_model.feature_importances_
features = X2_train.columns
feat_imp = pd.Series(importances, index=features).sort_values(ascending=False)

plt.figure(figsize=(8,5))
sns.barplot(x=feat_imp.values, y=feat_imp.index)
plt.title("Feature Importance (Location-Aware Model)")
plt.show()
```


	
![png](/images/blogs/adding_geospatial_features_to_ml_model/output_34_0.png)
	


This feature importance plot for our location-aware Airbnb price prediction model reveals the key drivers behind listing prices. Strikingly, the room_type feature stands out as overwhelmingly the most influential, suggesting that properties classified as hotel rooms have a distinct pricing structure that significantly impacts the model's predictions.

Following this, availability_365 and dist_nearest_park emerge as the next most significant factors. The importance of proximity to parks highlights how granular local amenities contribute to pricing, often more so than just a general dist_city_center. Other location-based features like density_1km and dist_nearest_subway also play their part, affirming the value of our location-aware approach.

### Visualizing results

Numbers are good, but maps make results tangible. Let’s map predicted prices from the location-aware model vs. the actual prices.


```python
import folium
import warnings
warnings.filterwarnings("ignore")

features_encoded = df_encoded_spatial.columns.to_list()
features_encoded.remove("price")

gdf_encoded = pd.get_dummies(gdf[spatial_features + ["price"]], drop_first=True)
gdf_encoded['latitude'] = gdf['latitude']
gdf_encoded['longitude'] = gdf['longitude']

# Create base map
m = folium.Map(location=[40.73, -73.93], zoom_start=11)

# Add predicted values as circle markers
for _, row in gdf_encoded.sample(500).iterrows():
	folium.CircleMarker(
		location=[row.latitude, row.longitude],
		radius=4,
		popup=f"Actual: ${row['price']} | Predicted: ${spatial_model.predict([row[features_encoded].values])[0]:.0f}",
		color="blue",
		fill=True,
		fill_opacity=0.6
	).add_to(m)

m.save("outputs/airbnb_predictions_map.html")
```

This lets us:
- Spot over- or under-valued areas (e.g., overpriced listings in less central areas).
- Show how predictions vary spatially, rather than just as numbers.
- Engage readers with an interactive, reality-grounded view.

> GeoAI is not only about better accuracy, but also about better understanding.

## Conclusion

In this blog, we built two machine learning models to predict Airbnb prices: one with standard features and another with added spatial context. The location-aware model outperformed the baseline, and the location-aware approach also highlighted something equally valuable: a property’s location shapes its value.

By engineering and analyzing spatial features, we saw how distance to the city center, nearby amenities, and neighborhood density influence pricing patterns. Beyond accuracy, this experiment shows that GeoAI isn’t just about building stronger models — it’s about adding a layer of geographic understanding that helps us interpret real-world dynamics.





***Author: Felix Do***