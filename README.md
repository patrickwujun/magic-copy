#扣出前置图像
# Magic Copy

Magic Copy is a Chrome extension that uses Meta's [Segment Anything Model](https://segment-anything.com/) to extract a foreground object from an image and copy it to the clipboard.

https://user-images.githubusercontent.com/511342/234897336-f2441e00-06e0-4ca8-9b97-601210879cdb.mp4

## Installation

[![Available on the Chrome Web Store](https://storage.googleapis.com/web-dev-uploads/image/WlD8wC6g8khYWPJUsQceQkhXSlv1/UV4C4ybeBTsZt43U4xis.png)](https://chrome.google.com/webstore/detail/nnifclicibdhgakebbnbfmomniihfmkg)

[![Available on the Firefox Add-ons Store](https://extensionworkshop.com/assets/img/documentation/publish/get-the-addon-178x60px.dad84b42.png)](https://addons.mozilla.org/en-US/firefox/addon/magic-copy/)

(This might not be available yet, as the extension is still in review. If you would like to be notified when they do, subscribe to the [Chrome](https://github.com/kevmo314/magic-copy/issues/16) or [Firefox](https://github.com/kevmo314/magic-copy/issues/17) issues.)

Alternatively, the extension can be installed manually:

1. Download the latest `magic-copy.zip` from [releases](https://github.com/kevmo314/magic-copy/releases).
2. Extract the ZIP file.
3. In Chrome, go to `chrome://extensions/`, enable "Developer mode", and click "Load unpacked".
4. Select the folder where the extension was extracted.

### Figma

Magic Copy is also available as a [Figma plugin](https://www.figma.com/community/plugin/1234171506421686729).

## Implementation

This extension uses the same procedure as the [Segment Anything Model demo](https://segment-anything.com/demo) to extract a foreground object from an image. The only difference is that the extracted object is copied to the clipboard instead of being displayed on the page.

## Building

### Manually

Build the extension with `npm` and then run the included `./buildcrx.sh` script to generate the `crx` file:

```sh
npm ci
npm run build
./buildcrx.sh -d dist
```

### Docker

A `Dockerfile` is provided to cleanly build the `crx` file. To build the extension, run:

```sh
docker build --output out .
```

## Self-hosting

The Meta Segment Anything Model requires running the vision transformer on a server
to generate the image's embeddings. Magic Copy uses the same service that their
demo uses, however some people may not want to send their images to a third party.

The `server-example` directory contains a simple example of how to self-host the
vision transformer service. It is not meant to be used in production, but rather as
a proof of concept to document the input/output format of the service.

In particular, Magic Copy (and the SAM demo) expect a POST endpoint that accepts
an image file and returns a JSON array of length 1 with the embedding of shape
`(1, 256, 64, 64)` as a base64 encoded string. See the code for specific details on how
to perform this encoding to be compatible with the demo.

If you are looking to quickly get the service running, you can use the provided
`Dockerfile` to build a container and run it. The container will expose port 8000
and will serve the service at the `/` endpoint.

```bash
docker build -t segment-anything .
docker run --gpus all -p 8000:8000 segment-anything
```

In the Magic Copy chrome extension, you can then change the endpoint to `http://localhost:8000/`.
