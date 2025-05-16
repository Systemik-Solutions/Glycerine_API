# IIIF API

The IIIF API from Glycerine Server utilises the IIIF Presentation Conventions. The major part of the API is about 
handling annotations, which implements the W3C [Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/).
Please refer to the [official documentation](https://www.w3.org/TR/annotation-protocol/) for more details about the 
annotation related API.

```{note}
The IIIF API from Glycerine Server is a minimal implementation of the Web Annotation Protocol. Some aspects such
as representation preferences and etag verification are not supported at this stage.
```

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
GET /iiif/annotation-collection/{id}
```

Get the IIIF Annotation Collection representation of an annotation set which serves as a container for the annotations.
For more details about the request and response, please refer to the 
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#container-retrieval) specification.

### URL parameters

- `id`: The ID of the annotation set.

## Get the annotation page

```
GET /iiif/annotation-collection/{id}/page
```

Get the IIIF Annotation Page representation of an annotation set. Note that annotation collection from Glycerine
Server always contains a single page. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#annotation-pages) specification.

### URL parameters

- `id`: The ID of the annotation set.

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

## Update an annotation

```
PUT /iiif/annotation/{id}
```

Update an annotation. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#update-an-existing-annotation) specification.

### URL parameters

- `id`: The ID of the annotation.

## Delete an annotation

```
DELETE /iiif/annotation/{id}
```

Delete an annotation. For more details about the request and response, please refer to the
[Web Annotation Protocol](https://www.w3.org/TR/annotation-protocol/#delete-an-existing-annotation) specification.

### URL parameters

- `id`: The ID of the annotation.
