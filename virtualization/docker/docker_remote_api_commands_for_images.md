# Docker Remote API Commands for Images

In the last post of this series, we discussed Docker Remote API and explored the commands specific to containers. In this post, let’s discuss commands specific to images.

## Create an Image

Images can be created in one of the following ways:

* By performing a registry pull
* By importing the image

  ```text
  POST /images/create
  ```

## Create an Image from a Container

To create an image from the container’s commits, use:

```text
POST /commit
```

## List of Images

To obtain the list of images, use:

```text
GET /images/json
```

## Insert a File

To insert a file at a specific path, use:

```text
POST /images/(name)/insert
```

## Delete Image

To delete an image by name, use:

```text
DELETE /images/(name)
```

## Registry Push

To push an image to the registry, use:

```text
POST /images/(name)/push
```

## Tag Image

To tag an image, use:

```text
POST /images/(name)/tag
```

## Search an Image

To search for an image, use:

```text
GET /images/search
```

## History

To view an image’s history, use:

```text
GET /images/(name)/history
```

## Build an Image

An image can be built with DockerFile using:

```text
POST /build
```

