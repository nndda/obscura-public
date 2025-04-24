# (Very) Quick Start

Requirements:
- A website/web project with images
- Obscura binary of your platform (Windows or Linux), and the JS libary

## Setting Up

Let's say you have a website like the following:
```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My epic website</title>
</head>

<body>
  <h1>Welcome to my epic website :)</h1>
  <p>Here's some of my epic illustration:</p>
  <img src="assets/image.png">
</body>

</html>
```

With the following structure:
```
my_website/
├─ assets/
│  └─ image.png
└─ index.html
```

First, lets separate the images from the website's project folder. Lets name it `my_images/`:
```
├─ my_website/
│  └─ index.html
│  
└─ my_images/
   └─ image.png
```

> [!NOTE]
> The setup used in this tutorial also available for download in the [itch.io page](https://nnda.itch.io/obscura)

## Configuration

Create a new YAML file under your images folder, this will be the Obscura configuration file. Lets name it `obscura.yaml`:
```
├─ my_website/
│  └─ index.html
│  
└─ my_images/
   ├─ image.png
   └─ obscura.yaml
```

On the YAML file, add the field `images`. This is a list of image files that you want to use on your site. The images' path should be relative to the YAML file.:
```yaml
images:
  - image.png
```

And then add `chunk_size` field. The images will be split to chunks of this dimension. e.g., If you have an image of the size 100x100, and the `chunk_size` is 50, it will then be split to 4 chunks. 

> [!IMPORTANT]
> The `chunk_size` value must be:
> - At least half the size of the image in both width and height (e.g., for a chunk of 80, the image must be at least 160×160).
> - Preferrably, a factor of the image size (e.g., for a 200×200 image, use 100 or 50, rather than 80).
> 
> Setting `chunk_size` too low will increase the images' fragments numbers, making the obfuscation more complex, but increases loading time, and damage your website's performance.

Let's assume `image.png` is 400x400, and set the `chunk_size` to 80. So, the image will split to 25 chunks:
```yaml
images:
  - image.png

chunk_size: 80
```

Lastly, add `output` field. This is a path to a folder (relative to the YAML file) where the images chunks data will be outputted. This should be placed where your website's assets located:
```yaml
images:
  - image.png

chunk_size: 80

output: ../my_website/img/
```
With `../my_website/img/`, we go up to the parent folder, and enters the website's folder. Note that there's also `img/` folder. This is a folder where every images' chunks will gets ouputted to (the folder will be created if not present). The parent of that folder—`my_website/`—will be where the JSON file that will be used for your website generated (explained more below).

> [!CAUTION]
> Make sure that you have a sub-folder like such. If you just set the output path as `../my_website/`, **the content of `my_website/` will be erased, and overwritten with the generated images chunks!!!**

## Generating the Data

Download the Obscura binary. Run it in your terminal to confirm it works:
```
path/to/obscura --version
```
It should output:
```
obscura v0.1.0
```

After you confirm that the binary is working, run it with the path to the YAML as the first argument (assuming the terminal opens in our project folder), we'll run it like:
```
path/to/obscura my_images/obscura.yaml
```

After it finished, there should be:
- A new file called `obscura.json.gz`, under the `my_website/` folder.
- Images' chunks under the `my_website/img/` folder.

These files will be the one used in the website.

## Website Implementation

Now, the files—ones in `my_website/`—should looks like this:
```
└─ my_website/
   ├─ img/
   │  ├─ ...
   │  ├─ ...
   │  └─ ...
   ├─ index.html
   └─ obscura.json.gz
```

Put the Obscura web library—`obscura.min.js`—on the website folder:
```
└─ my_website/
   ├─ img/
   │  ├─ ...
   │  ├─ ...
   │  └─ ...
   ├─ index.html
   ├─ obscura.json.gz
   └─ obscura.min.js
```

And then link it on the `index.html`. In `<head>`, _or_ in `<body>`:
```html
<script src="obscura.min.js"></script>
```

Create another `<script>` tag after it, and call `Obscura()` function, with the path pointing to the JSON file mentioned before as the first argument:
```html
<script src="obscura.min.js"></script>
<script>
  Obscura("obscura.json.gz")
</script>
```

Now, you can add the obfuscated image on your website using the `<img>` tag, with `data-obscura` attribute as the filename of the image without the file etension.
Since our image were named `image.png`, we'll use `image`:
```html
<img data-obscura="image">
```

Start a local server on your site to test it. If you use Python, you can use:
```sh
python -m http.server
```

---

> [!NOTE]
> If you're using version control, **do not** commit the following:
> - Obscura binaries
> - Generated JSON file, and the images' chunks.
