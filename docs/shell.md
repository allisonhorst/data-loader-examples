# Shell script data loader examples

In Observable Framework, [data loaders](../loaders) can be created in shell scripts — they will be called with the system shell "sh".

## Parquet

The data loader below accesses data on alternative fuel stations from the [U.S. Department of Energy](https://catalog.data.gov/dataset/alternative-fueling-station-locations-422f2), simplifies to only California stations in SQL, then returns an Apache Parquet file.

Create a file in your project source root with the .parquet.sh double extension (for example, `docs/data/my-data.parquet.sh`), then paste the code below to get started.

<!-- TODO this one needs explaining. -->

```js
showCode(FileAttachment("data/alt-fuel-stations.parquet.sh"))
```

Then, to access the output (`data/alt-fuel-stations.parquet`) in a page, we create a helper function:

```js echo
function absoluteFA(FA) {
  const {url} = FA;
  FA.url = async function() {
    return new URL(await url.apply(FA), document.location.href).href;
  }
  return FA;
}
```

And read in the file using `FileAttachment`:

```js echo
const caAltFuel = await DuckDBClient.of({
  fuelstations: absoluteFA(FileAttachment("data/alt-fuel-stations.parquet"))
});
```

```js echo
caAltFuel
```

```js echo
const fuelTable = caAltFuel.query("SELECT * FROM fuelstations");
```

```js echo
Inputs.table(fuelTable)
```

## JSON

Sometimes, all you need is `curl`!

The data loader below accesses geojson of CalTrans districts from the [California Open Data Portal](https://data.ca.gov/dataset/caltrans-districts/resource/668dacf7-e927-4ced-98aa-b495e79d40d2).

Create a file in your project source root with the .json.sh double extension (for example, `docs/data/my-data.json.sh`), then paste the code below to get started.

```js
showCode(FileAttachment("data/caltrans-districts.json.sh"))
```

Access the output of the data loader from the client using [`FileAttachment`](https://observablehq.com/framework/javascript/files):

```js echo
const caltrans = FileAttachment("data/caltrans-districts.json").json()
```

<p class="tip">The file attachment name does not include the <tt>.sh</tt> extension. We rely on Framework’s <a href="https://observablehq.com/framework/routing">routing</a> to run the appropriate data loader.

We can now explore the JSON output:

```js echo
caltrans
```

## CSV

Working in a shell script is flexible. Within the shell script, work in whatever you language you like to access and prep your data, then write to standard output.

The data loader example below starts a Python script, accesses the [penguins data](https://journal.r-project.org/articles/RJ-2022-020/) data from a local file and does some basic wrangling, then writes a CSV to standard output.

Create a file in your project source root with the .csv.sh double extension (for example, `docs/data/my-data.csv.sh`), then paste the code below to get started.

```js
showCode(FileAttachment("data/penguin.csv.sh"))
```

Access the output of the data loader from the client using [`FileAttachment`](https://observablehq.com/framework/javascript/files):

```js echo
const penguins = FileAttachment("data/penguin.csv").csv({typed: true})
```

<p class="tip">The file attachment name does not include the <tt>.sh</tt> extension. We rely on Framework’s <a href="https://observablehq.com/framework/routing">routing</a> to run the appropriate data loader.

```js echo
Inputs.table(penguins)
```

```js
import {showCode} from "./components/showCode.js";
```
