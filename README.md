# GEE_Unsupervised_Classification

## Define a region in which to generate a sample of the input.

## Sample region
```js
var reg = 
    /* color: #ff0000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[-14.44038255510311, 15.433926892817814],
          [-14.44038255510311, 12.212626071156977],
          [-8.24409349260311, 12.212626071156977],
          [-8.24409349260311, 15.433926892817814]]], null, false);
          
```
## Senegalese boundary in which i display unsupervised classification
```js
// Data Fusion country in the world
var countries = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017');
// Clip senegalese Boundary
var Senegal= countries.filter(ee.Filter.eq('country_na', 'Senegal'));
```

## Load a pre-computed Landsat composite for input.
**1.** Call data collection and pick up one image 
```js
var input = ee.Image(ee.ImageCollection('LANDSAT/LE7_TOA_1YEAR').filterBounds(Senegal).first());
```

**2.** Take a look in parameter of image
```js
print(input)
```
##  Display the senegal boundary.
```js
Map.centerObject(Senegal,6);
Map.addLayer(ee.Image().paint(Senegal, 0, 2), {}, 'region');
```

## Make the training dataset.

```js
var training = input.sample({
  region: reg,
  scale: 30,
  numPixels: 5000
});
```
```js
print(training)
```

## Instantiate the clusterer and train it.
**1.**
```js
var clusterer = ee.Clusterer.wekaKMeans(5).train(training);
```
**2.**

// Cluster the input using the trained clusterer and clip with boundary of senegal.
```js
var result = input.cluster(clusterer).clip(Senegal);
```
## Display the clusters with random colors.
```js
Map.addLayer(result.randomVisualizer(), {}, 'clusters');
```
