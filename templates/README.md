# Mangal submission templates

This folder stores a series of `.csv` files, which, when filled, represent an
almost complete data submission to <mangal.io> (the remaining steps are to add
metadata when opening the *Data Submission* [issue]).

## A brief overview of the format

The data in mangal are represented by a hierarchy of types, and some choices
deserve a clarification. Throughout this section, fields in `monospace` refer to
how the database and API actually calls these things, and you can look at the
endpoint examples to see how the data are actually shaped (and endpoint is the
URL used to access the data, and you don't really need to know this in order to
prepare your files). In practice, the data representation has a lot more fields
than that, but they are populated automatically to ensure that the data are
correctly linked.

### Datasets

The highest-level unit is a `datataset` (see the [dataset] endpoint for
examples). A dataset contains high-level metadata, and is intended as a
collection of networks. Some datasets store a single network, some store
hundreds. Think of a dataset as one paper. The fields in a dataset are as
follows:

~~~json
{
    "name":"howking_1968",
    "description":"Insect activity recorded on flower at Lake Hazen, Ellesmere Island, N.W.T., Canada",
    "ref_id":2
}
~~~

With the exception of `ref_id` (which is a `reference`, see the [reference]
endpoint for an example), the two fields are mandatory; `name` must be unique
across the entire database, and `description` can be arbitrarily long.

The `dataset` level does not have a `csv` template, as we will ask for the
information during the *Data Submission* issue.

⚠️ **It is very important** that a *Data Submission* cover a *single* dataset!

### Networks

Within a `dataset` is a series of `network`s (see the [network] endpoint for
examples)

~~~json
{
    "name":"roberson_1929_18990701_19",
    "date":"1899-07-01T00:00:00.000Z",
    "geom":{
        "type":"Point",
        "coordinates":[-89.8818,39.2798]
    },
    "description":"Insects observed pollinating flowers, ten miles of Carlinville, Illinois, USA",
    "all_interactions":false
}
~~~

The `name` must also be unique, and is usually the dataset name followed by
additional information (in the case, the date as `YYYYMMDD` and the network
number); this is an example of a dataset with more than one network in it. The
`description` field is to provide specific information about *this* network.

📆 The `date` field is *optional*. If there is a single collection day for the
network, you can indicate it here, or you can provide the month of collection as
`YYYY-MM-01`. In a majority of cases, network collection takes more than a
single day, and the interaction object will have the correct date/time with a
better granularity. This being said, storing networks is cheap. If you have
sampled the same locations three times in a week, it is perfectly fine to turn
this into three networks, as opposed to have a single network with interactions
at different times; this is likely a discussion we will have when looking at the
submitted data.

🌍 The `geom` field is a [GeoJSON][geojson] `geometry`, which will often be a
point, but can also a polygon. For example, the food web of [Angolan fisheries
landing][angola] is defined as polygon represented by

~~~json
"geom":{
    "type":"Polygon",
    "coordinates":[
        [
            [10.9533691,-17.25],
            [11.6235352,-17.25],
            [11.6455078,-15.5383759],
            [12.1289063,-13.9660541],
            [13.6010742,-11.1352871],
            [11.953125,-5],
            [10.7006836,-5],
            [10.9533691,-17.25],
            [10.9533691,-17.25]]
    ]
}
~~~

In all cases, the pairs of points are given as `[longitude, latitude]`. In the
template, a polygon is represented as a *closed* object:

~~~csv
geometry;coordinates
polygon;[[x,y],[w,z],[k,j],[x,y]]
~~~

whereas a point is simply

~~~csv
geometry;coordinates
point;[x,y]
~~~

Within a dataset, networks can be a mix of points and polygons, but each network
must be a single type.

🔬 The `all_interactions` field is a flag indicating whether the dataset
contains *known* non-interactions. This is going to be `false` about 99% of the
time, but may be `true` when dealing with infection assays, cafeteria
experiments, etc. There is a special interaction-level flag to indicate which
are know to not occur.

<!-- links -->

[issue]: https://github.com/mangal-interactions/contribute/issues/new/choose
[dataset]: https://mangal.io/api/v2/dataset
[geojson]: https://geojson.org/
[angola]: https://mangal.io/api/v2/network/?q=Angolan%20fishery
[network]: https://mangal.io/api/v2/network
[interaction]: https://mangal.io/api/v2/interaction
[node]: https://mangal.io/api/v2/node
[taxonomy]: https://mangal.io/api/v2/taxonomy
[reference]: https://mangal.io/api/v2/reference