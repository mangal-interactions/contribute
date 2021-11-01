# Mangal submission templates

This folder stores a series of `.csv` files, which, when filled, represent an
almost complete data submission to mangal.io (the remaining steps are to add
metadata when opening the *Data Submission* [issue]).

⚠️ **We use `;` as a delimiter for the CSV files**. This is because the `,`
character is expected to occur more commonly both in descriptions and in
geographic coordinates.

The data in mangal are represented by a hierarchy of types, and some choices
deserve a clarification. This section will provide a detailed explanation of
what makes a mangal object, and then give a link to the template. All the
templates are organised in the same way: the first row is the field name, the
second row tells whether it is required or optional, and the last row is
reminders about how to format the data.

🔭 A 100000ft. view of the data organisation, and of the relationship between the elements, is:

- datasets contains networks
- networks contains nodes
- nodes are linked to a taxonomy backbone
- interactions are linking two nodes
- references, traits, and environment metadata are attached to various objects

Here is a checklist of **mandatory** files:

- [ ] `dataset.csv`
- [ ] `networks.csv`
- [ ] `nodes.csv`
- [ ] `interactions.csv`

Here is a checklist of **optional** files:

- [ ] `environment.csv`
- [ ] `traits.csv`

⚠️ When filling-in the templates, **please do not remove** rows 2 and 3; they
are used in the automatic validation we perform before moving on to manual
inspection. Your data should always start on row 4. The column delimitor is `;`.

Throughout this section, fields in `monospace` refer to how the database and API
actually calls these things, and you can look at the endpoint examples to see
how the data are actually shaped (and endpoint is the URL used to access the
data, and you don't really need to know this in order to prepare your files). In
practice, the data representation has a lot more fields than that, but they are
populated automatically to ensure that the data are correctly linked.

⚠️ When downloading the templates, **please do not rename them**. This is going
to help with the injection steps, as we perform some automatic checks for data
integrity before manual inspection.

🤓 If you want to take a really deep dive at the data representation, the API
documentation is [available online][API], and will let you look at the raw JSON
objects representing each data type. We strongly suggest you do not read it.

## Datasets

The highest-level unit is a `datataset` (see the [dataset] endpoint for
examples). A dataset contains high-level metadata, and is intended as a
collection of networks. Some datasets store a single network, some store
hundreds. Think of a dataset as one paper. The fields in a dataset are as
follows (the data are presented as a table here, but in practice they are stored
a JSON objects):

| Field       | value                                                                              |
| ----------- | ---------------------------------------------------------------------------------- |
| name        | howking_1968                                                                       |
| description | Insect activity recorded on flower at Lake Hazen, Ellesmere Island, N.W.T., Canada |

The two fields are mandatory; `name` must be unique across the entire database,
and `description` can be arbitrarily long. The `dataset` level does not have a
`csv` template, as we will ask for the information during the *Data Submission*
issue.

⚠️ **It is very important** that a *Data Submission* cover a *single* dataset!
If your dataset contains multiple networks, you only need to submit one file for
the networks, nodes, interactions, etc.

## Networks

Within a `dataset` is a series of `network`s (see the [network] endpoint for
examples).

➡️ [Get the template](https://raw.githubusercontent.com/mangal-interactions/contribute/main/templates/networks.csv)

| Field            | Value                                                                         |
| ---------------- | ----------------------------------------------------------------------------- |
| name             | roberson_1929_18990701_19                                                     |
| date             | 1899-07-01T00:00:00.000Z                                                      |
| geom             | `"type":"Point", "coordinates":[-89.8818,39.2798]`                            |
| description      | Insects observed pollinating flowers, ten miles of Carlinville, Illinois, USA |
| all_interactions | false                                                                         |

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
landing][angola] is defined as polygon, and its `"geom"` is represented by

~~~json
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
~~~

In all cases, the pairs of points are given as `[longitude, latitude]`. In the
template, a polygon is represented as a *closed* object, so the first and last
point should be equal:

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
must be a single type. If you want to draw your polygon, <geojson.io> is very
simple to use. A polygon is often a good type of geometry to use: interaction
objects have their own geom field, and so you can represent the network as
covering, *e.g.* a forest fragment with a polygon, and then locate each
interaction with a point within it.

🔬 The `all_interactions` field is a flag indicating whether the dataset
contains *known* non-interactions. This is going to be `false` about 99% of the
time, but may be `true` when dealing with infection assays, cafeteria
experiments, etc. There is a special interaction-level flag to indicate which
are known to not occur.

## Environmental conditions (optional)

Environmental conditions have a "one-to-many" relationship with networks; in
short, you can have multiple variables associated to a single network, such as
for example elevation, temperature, and soil moisture.

➡️ [Get the template](https://raw.githubusercontent.com/mangal-interactions/contribute/main/templates/environment.csv)

The mandatory traits are `network` (the *name* of a network in the
`networks.csv` file); other fields are self-explanatory, and the unit will
default to `None` for dimensionless variables.

## Taxonomy and nodes

A `network` is a collection of `node`s (see the [node] endpoint) and
`interaction`s, but we always start by uploading the nodes. Nodes are actually
two objects wearing a trenchcoat: the node itself as *you* called it in the raw
data, and an object in the `taxonomy` backbone (see the [taxonomy] endpoint).

➡️ [Get the template](https://raw.githubusercontent.com/mangal-interactions/contribute/main/templates/nodes.csv)

😕 **Wait, what**? Taxonomic identifications in the field are not necessarily as
precise as we would like, and we want to strike the right balance between (i)
keeping the data as raw as possible, for reproducibility, and (ii) linking them
to a valid taxonomic node, for generality. For example, let's look at this node:

| Field         | value                                |
| ------------- | ------------------------------------ |
| original_name | Z1ai: copepods (large, >= 0.025mg C) |
| node_level    | taxon                                |
| network_id    | 2470                                 |
| taxonomy_id   | 4441                                 |

It maps to the following entry in our taxonomy:

| Field | value    |
| ----- | -------- |
| name  | Copepods |
| ncbi  | 830      |
| tsn   | 5257     |

This way, we can map the `node` to the actual network node in the raw data, but
we can also look for *e.g.* "all interactions involving copepods".

To facilitate the injection of nodes, we put information for original and
reference names in the same template.

`original_name` is the name that you gave the node in your original data. It
does not need to be globally unique, but *it needs to be unique within a
network*. In the template, you will see that we do not ask for `network_id`
(instead using the network *name*), nor for `taxonomy_id` (as we will match or
create the taxonomy backbone entry as required).

The `node_level` field is one of `taxon`, `population` or `individual` - in
practice, `population` and `taxon` are more or less interchangeable, the only
recommendation we offer is that if you have node `attribute`s (these are covered
later), `population` is *probably* more accurate than `taxon`. If the
information you have is *I have a reference that says Lynx eat hares*, this is
probably a `taxon` level interaction.

💬 As for other choices where there is no *correct* solution, we will discuss
the consequences of this choice as part of  the *Data Submission* issue.

The `name` field is the valid taxonomic name assigned to the node. The other
fields for the taxonomy are identifiers in various databases:

| field  | data source               |
| ------ | ------------------------- |
| `ncbi` | ncbi.nlm.nih.gov/taxonomy |
| `tsn`  | itis.gov                  |
| `eol`  | eol.org                   |
| `bold` | ibol.org                  |
| `gbif` | gbif.org                  |
| `col`  | catalogueoflife.org       |

They are *all* optional and we will try to populate at least the GBIF and NCBI
ones; in cases of name ambiguity, we will populate at least one of these to
ensure there is no key conflict.

Finally, the `rank` field (optional) is an indication of the taxonomic rank of
the node; this is being progressively phased out in support of querying
information using taxonomic identifiers, but the accepted values are `kingdom`,
`subkingdom`, `infrakingdom`, `superdivision`, `division`, `subdivision`,
`phylum`, `class`, `superorder`, `order`, `superfamily`, `family`, `genus`,
`subgenus`, `species`, and `infraspecies`.

## Node traits (optional)

Node traits conditions have a "one-to-many" relationship with nodes; in short,
you can have multiple traits associated to a single node, such as for example
body size, life stage, and color.

➡️ [Get the template](https://raw.githubusercontent.com/mangal-interactions/contribute/main/templates/traits.csv)

The mandatory traits are `network` (the *name* of a network in the
`networks.csv` file) and `node` (the *original name* of the node in the
`nodes.csv` file); other fields are self-explanatory, and the unit will default
to `None` for dimensionless variables.

## Interactions

The `interaction` object is what links nodes from a network together. As such,
this is likely going to be the largest file you submit. If you submit multiple
networks at once, they should still be in the *same* `interactions.csv` file.

➡️ [Get the template](https://raw.githubusercontent.com/mangal-interactions/contribute/main/templates/interactions.csv)

Let's look at an example:

| Field      | value                    |
| ---------- | ------------------------ |
| network_id | 1474                     |
| node_from  | 24589                    |
| node_to    | 24554                    |
| date       | 1983-01-01T00:00:00.000Z |
| direction  | directed                 |
| type       | herbivory                |
| method     | gut content              |
| value      | 8                        |


Interactions are a mapping between two `node`s from a `network`, and the rest is
biological metadata. Interactions can have their own `date` and `geom` (which follow the same logic as the one for `network`s). 

The `direction` field can be `undirected`, `directed`, or `unknown`; it is
mandatory.

The `type` is a controlled vocabulary, which can be one of `competition`,
`amensalism`, `neutralism`, `commensalism`, `mutualism`, `parasitism`,
`predation`, `herbivory`, `symbiosis`, `scavenger`, `detritivore`,
`unspecified`, `consumption`. The default is `unspecified`. This vocabular can
be expanded upon request.

The `method` field is free-form text to indicate how the observation was
established; this will most often be `observation`, but can also be *e.g.*
`experiment`, `inference`, `literature`, etc.

The `value` field is the *strength* of the interaction, and it is represented
exactly like traits and environmental variables in the template (under the hood,
this involves writing things in a few different tables, but for the purpose of
submitting data, it is not).

## Submission

When all the appropriate files are ready, you can initiate a *Data Submission*
[issue] to upload them, add some metadat for the dataset, and mention any
relevant information / ask any question you have. We will then clarify the
details of the dataset, and take care of the upload for you.

<!-- links -->

[API]: https://mangal-interactions.github.io/mangal-api/
[issue]: https://github.com/mangal-interactions/contribute/issues/new/choose
[dataset]: https://mangal.io/api/v2/dataset
[geojson]: https://geojson.org/
[angola]: https://mangal.io/api/v2/network/?q=Angolan%20fishery
[network]: https://mangal.io/api/v2/network
[interaction]: https://mangal.io/api/v2/interaction
[node]: https://mangal.io/api/v2/node
[taxonomy]: https://mangal.io/api/v2/taxonomy
[reference]: https://mangal.io/api/v2/reference