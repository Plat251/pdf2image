# pdf2image [![TravisCI](https://travis-ci.org/Belval/pdf2image.svg?branch=master)](https://travis-ci.org/Belval/pdf2image) [![PyPI version](https://badge.fury.io/py/pdf2image.svg)](https://badge.fury.io/py/pdf2image) [![codecov](https://codecov.io/gh/Belval/pdf2image/branch/master/graph/badge.svg)](https://codecov.io/gh/Belval/pdf2image) [![Downloads](https://pepy.tech/badge/pdf2image/month)](https://pepy.tech/project/pdf2image) [![mattermost](https://img.shields.io/badge/help-mattermost-blue)](https://mattermost.belval.org/signup_user_complete/?id=3anaj34563rqjg4xm5tdcmu7qa)
A python 2.7 and 3.5+ module that wraps pdftoppm and pdftocairo to convert PDF to a PIL Image object

## How to install

`pip install pdf2image`

### Windows

Windows users will have to install [poppler for Windows](http://blog.alivate.com.au/poppler-windows/), then add the `bin/` folder to [PATH](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/).

### Mac

Mac users will have to install [poppler for Mac](http://macappstore.org/poppler/).

### Linux

Most distros ship with `pdftoppm` and `pdftocairo`. If they are not installed, refer to your package manager to install `poppler-utils`

### Platform-independant (Using `conda`)

1. Install poppler: `conda install -c conda-forge poppler`
2. Install pdf2image: `pip install pdf2image`

## How does it work?

`from pdf2image import convert_from_path, convert_from_bytes`

```py
from pdf2image.exceptions import (
    PDFInfoNotInstalledError,
    PDFPageCountError,
    PDFSyntaxError
)
```

Then simply do:

```py
images = convert_from_path('/home/belval/example.pdf')
```

OR

```py
images = convert_from_bytes(open('/home/belval/example.pdf', 'rb').read())
```

OR better yet

```py
import tempfile

with tempfile.TemporaryDirectory() as path:
    images_from_path = convert_from_path('/home/belval/example.pdf', output_folder=path)
    # Do something here
```

`images` will be a list of PIL Image representing each page of the PDF document.

Here are the definitions:

`convert_from_path(pdf_path, dpi=200, output_folder=None, first_page=None, last_page=None, fmt='ppm', thread_count=1, userpw=None, use_cropbox=False, strict=False, transparent=False, single_file=False, output_file=str(uuid.uuid4()), poppler_path=None, grayscale=False)`

`convert_from_bytes(pdf_file, dpi=200, output_folder=None, first_page=None, last_page=None, fmt='ppm', thread_count=1, userpw=None, use_cropbox=False, strict=False, transparent=False, single_file=False, output_file=str(uuid.uuid4()), poppler_path=None, grayscale=False)`

## Need help?

Use the [mattermost chat](https://mattermost.belval.org/signup_user_complete/?id=3anaj34563rqjg4xm5tdcmu7qa) to ask questions on the helpdesk and get direct support.

## What's new?

- `grayscale` parameter allows you to convert images to grayscale (`-gray` in pdftoppm CLI)
- `single_file` parameter allows you to convert the first PDF page only, without adding digits at the end of the `output_file`
- Allow the user to specify poppler's installation path with `poppler_path`
- Fixed a bug where PNGs buffer with a non-terminating I-E-N-D sequence would throw an exception
- Fixed a bug that left open file descriptors when using `convert_from_bytes()` (Thank you @FabianUken)
- `fmt='tiff'` parameter allows you to create .tiff files (You need pdftocairo for this)
- `transparent` parameter allows you to generate images with no background instead of the usual white one (You need pdftocairo for this)
- `strict` parameter allows you to catch pdftoppm syntax error with a custom type `PDFSyntaxError`

## Performance tips

- Using an output folder is significantly faster if you are using an SSD. Otherwise i/o usually becomes the bottleneck.
- Using multiple threads can give you some gains but avoid more than 4 as this will cause i/o bottleneck (even on my NVMe SSD!).
- If i/o is your bottleneck, using the JPEG format can lead to significant gains.
- PNG format is pretty slow, this is because of the compression.
- If you want to know the best settings (most settings will be fine anyway) you can clone the project and run `python tests.py` to get timings.

## Limitations / known issues

- A relatively big PDF will use up all your memory and cause the process to be killed (unless you use an output folder)
