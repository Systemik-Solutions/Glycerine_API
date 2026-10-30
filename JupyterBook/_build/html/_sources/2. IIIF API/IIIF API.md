# IIIF API

The IIIF API from Glycerine Server utilises the IIIF Presentation Conventions. The major part of the API is about 
handling annotations, which implements the W3C [Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/).
Please refer to the [official documentation](https://www.w3.org/TR/annotation-protocol/) for more details about the 
annotation related API.

```{note}
The IIIF API from Glycerine Server is a minimal implementation of the Web Annotation Protocol. Some aspects such
as representation preferences and etag verification are not supported at this stage.
```
## Get Collection manifest

```
GET /iiif/collection/{id}
```

Get the IIIF Collection manifest from a collection. Note that this requires the request to be authenticated, which is
different from the collection publication manifest.

### URL parameters

- `id`: The ID of the collection.

## Get IIIF manifest

```
GET /iiif/manifest/{id}
```

Get the IIIF manifest from an image set. Note that this requires the request to be authenticated, which is different
from the image set publication manifest. Also note that the manifest returned from this API doesn't include the 
annotations.

### URL parameters

- `id`: The ID of the image set.

### Response

The IIIF manifest of the image set.

## Get the canvas

```
GET /iiif/canvas/{id}
```

Get the IIIF Canvas representation of an image. Not that it includes the annotations from all annotation sets which
the current authenticated user has access to, including all the published annotation sets.

### URL parameters

- `id`: The ID of the image.

### Response

The IIIF Canvas representation of the image.

## Get the annotation collection

```
GET /iiif/annotation-collection/{id}[?pageby=canvas]
```

Get the IIIF Annotation Collection representation of an annotation set which serves as a container for the annotations.
For more details about the request and response, please refer to the 
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#container-retrieval) specification.

By default, all annotations from the annotation set will be under a single annotation page, which can improve the
loading performance. If the `pageby` query parameter is set to `canvas`, the annotations will be grouped into pages by
each canvas. For annotations without an `image_id`, pages are grouped by the source URI, extracted from either
a URI string or an identified source object. A `SpecificResource` target's own `id` is not used as its source
grouping key. Whole-image and region annotations for the same associated image appear on the same image page.

### URL parameters

- `id`: The ID of the annotation set.

## Get the annotation page

```
GET /iiif/annotation-collection/{id}/page/{target}
```

Get an IIIF Annotation Page representation from an annotation set. For more details about the request and response,
please refer to the [Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#annotation-pages) 
specification.

### URL parameters

- `id`: The ID of the annotation set.
- `target`: The annotation target which can be an ID of the image if the annotations are targeting to images from 
  Glycerine Server, or the MD5 hash of the annotation target URI. Or if it is set to `all`, all annotations in the
  annotation set will be returned.

For targets without a local image association, hash the normalized source URI, not the serialized target
object or a `SpecificResource` target's own `id`. If a rectangular URI fragment was normalized into a selector,
use the source URI without that fragment. Follow the collection/page links returned by the server when possible.

## Get the annotation

```
GET /iiif/annotation/{id}
```

Get the IIIF Annotation representation of an annotation. For more details about the request and response, please refer
to the [Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#annotation-retrieval) specification.

### URL parameters

- `id`: The ID of the annotation.

## Create an annotation

```
POST /iiif/annotation-collection/{id}
```

Create an annotation in an annotation set. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#create-a-new-annotation) specification.

### URL parameters

- `id`: The ID of the annotation set.

### Request

Send a Web Annotation object with:

- `type`: (required) `Annotation`.
- `body`: (required) An array of annotation bodies. An empty array is accepted.
- `generator`: (required on creation) The URI identifying an existing annotation template, ending in
  `/annotation-template/{publication_id}`.
- `target`: (required) One of the {ref}`supported annotation target inputs <annotation-target>`. A selector is optional.

Example of a whole-image annotation (replace the illustrative target and template URIs):

```json
{
    "type": "Annotation",
    "generator": "https://glycerine.example/api/publications/annotation-template/template-id",
    "body": [],
    "target": "https://glycerine.example/api/iiif/canvas/42"
}
```

The same target normalization and validation rules apply to REST and IIIF writes. A valid target is saved as
a `SpecificResource` object. URI rectangles become FragmentSelectors; selectorless targets remain selectorless.

### Image association

IIIF creation resolves `image_id` from the normalized source. URI strings, identified resource objects, and
source-based targets with string or identified-object sources use the same resolution rules.

An image is associated only when the source exactly matches the server-generated Canvas URI for an image
belonging to the annotation set's image set. A foreign URL with the same `/iiif/canvas/{id}` suffix is not
treated as a local image. Valid external targets, unmatched Canvas URIs, and targets in standalone annotation
sets retain a null `image_id`. No image ID needs to be supplied in the IIIF request.

When associated, `image_id` determines the Canvas source in IIIF output.

### Response

HTTP `201` with the created Web Annotation representation. The normalized target may differ in shape from the
submitted target. The response includes `Location` and `ETag` headers.

Invalid annotation format, target, or template returns HTTP `400` with a `message`. Invalid targets are rejected
before saving the annotation or changing fields and tags.

## Update an annotation

```
PUT /iiif/annotation/{id}
```

Update an annotation. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#update-an-existing-annotation) specification.

### URL parameters

- `id`: The ID of the annotation.

### Request

Send a Web Annotation object with `type: "Annotation"`, a `body` array, and a required `target`, using the
{ref}`same target formats and validation rules <annotation-target>` as creation. Include the template `generator`
when sending template field bodies.

Unlike a REST partial update, an IIIF update requires the target on every request. The target is normalized and
the local image association is resolved again using the same rules as IIIF creation. Invalid targets are
rejected before changing the annotation, fields, or tags.

The update replaces the annotation's fields and tags using the supplied bodies; send the complete bodies you
want to retain.

### Response

HTTP `201` with the updated Web Annotation representation and an `ETag` header. This endpoint currently returns
`201` on successful updates as well as creation.

Missing or invalid targets return HTTP `400` with a `message`.

## Delete an annotation

```
DELETE /iiif/annotation/{id}
```

Delete an annotation. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#delete-an-existing-annotation) specification.

### URL parameters

- `id`: The ID of the annotation.
