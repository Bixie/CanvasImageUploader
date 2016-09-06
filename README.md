# CanvasImageUploaderPlus
Resize and rotate images by EXIF orientation on the client side during upload. This uses the HTML Canvas element and HTML5 FileReader.
Forked from [Anton Jansson](https://github.com/Bixie/CanvasImageUploader)

## Use
Require from npm: `npm install https://github.com/Bixie/CanvasImageUploader --save`

Or, if you just want to include the Javascript file in your HTML, make sure to also include [https://github.com/jseidelin/exif-js](https://github.com/jseidelin/exif-js), before CanvasImageUploaderPlus.
    
    <script src="exif-js/exif.js"></script>
    <script src="canvas-image-uploader.js"></script>

### Basic file upload
Upload image to canvas via file input.

    <input type="file" name="file" id="file">

Javascript:

```javascript
    var uploader = new CanvasImageUploaderPlus({
        maxSize: 1500,
        jpegQuality: 0.7
    });
    
    $('#file').bind('change', function onImageChanged(e) {
        var files = e.target.files || e.dataTransfer.files;
        if (files) {
            file = files[0];
            var $canvas = $('<canvas>');
            uploader.readImageToCanvas(file, $canvas, function () {
                uploader.saveCanvasToImageData($canvas[0]);
            });
        }
    });
    
    // Upload the file data
    function onFormSubmit() {
        $.ajax({
            type: 'POST',
            url: 'http://...',
            data: uploader.getImageData(),
            beforeSend: function (request) {
                request.setRequestHeader("Content-Type", ".jpg");
            },
            processData: false,
            success: function (result) {
            },
            error: function (error) {
            }
        });
    }
```

### Show a preview of the image
Add a preview canvas to your HTML.

    <canvas id="preview-canvas" height="0" width="0"></canvas>

Javascript:

```javascript
    uploader.readImageToCanvas(file, $canvas, function () {
        var canvas = $canvas[0];
        // Render the preview from your original canvas...
        uploader.copyToCanvas(canvas, $('#preview-canvas'), MAX_PREVIEW_SIZE);
        uploader.saveCanvasToImageData(canvas);
    });
```

### Load existing image
Add an existing image to the canvas without upload.

    <canvas id="preview-canvas" height="0" width="0"></canvas>

Javascript:

```javascript
    function addImageToCanvas($canvas, image_url, width, cb) {
        var image = new Image();
        var $cnvs = jQuery('<canvas>');
        image.onload = () => {
            uploader.setImageToCanvas(image, $cnvs, () => {
                var canvas = $cnvs[0];
                // Render the preview from your original canvas...
                uploader.copyToCanvas(canvas, $canvas, width || 300);
                uploader.saveCanvasToImageData(canvas);
                cb();
            });
        };
        image.onerror = () => console.log('Error loading image');
        image.src = image_url;
```