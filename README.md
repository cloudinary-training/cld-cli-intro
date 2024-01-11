# Introduction to Cloudinary's CLI

Cloudinary offers a command line interface (CLI) that enables you to make API calls directly in your terminal.
The Cloudinary CLI runs on top of the Python SDK.

# Course Outline:

The information in this repository's README follows along the structure of the Cloudinary Academy course, [Introduction to Cloudinary's CLI](https://training.cloudinary.com/learn/course/introduction-to-cloudinarys-cli-one-hour-course/lessons/setting-up-your-environment-0820) and even includes some examples for topics we do not cover in the Introduction to Cloudinary's CLI Course, such as Presets, Named Transformations, Backups, and Auto-Upload mapping. 

If you would like to take this 50 minute course, you can enroll for free at [training.cloudinary.com](https://training.cloudinary.com).

The information below corresponds with the [slides](https://cloudinary-marketing-res.cloudinary.com/image/upload/v1704049241/Introduction-Cloudinary-CLI.pdf) used in [Introduction to Cloudinary's CLI](https://training.cloudinary.com/learn/course/introduction-to-cloudinarys-cli-one-hour-course). 

Course Topics:
- Introduction: 2:42 minutes
- Environment Setup: 3:38 minutes
- Helper Tools: 1:36 minutes
- Uploading: 12:07 minutes
- Cloudinary URLs: 2:31 minutes
- Managing Assets: 11:50 minutes
- Sync: 3:11 minutes
- Transformations: 10:02 minutes
- Further Support: 1:53 minutes


---

## Setup Environment

### Install Python

- Python 3.6 or greater
- Download: https://www.python.org/downloads/
- Mac : brew install recommended https://brew.sh/

#### Installing Python on Mac with Homebrew

```bash
# install brew
mkdir homebrew && curl -L https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C homebrew

# install python
brew install python3
```

### Install Cloudinary

**Initial install:**

```bash
pip3 install cloudinary-cli
```

**Update Cloudinary Package:**

```bash
 pip3 install cloudinary-cli --upgrade
 ```

### Cloudinary Credentials

### Setup Environment Variables

Sign up for free account or login.

Navigate to:  [https://cloudinary.com/console](https://cloudinary.com/console)

Once you have a Cloudinary account, you can access your CLOUDINARY_URL/API Environment Variable in your Dashboard, as shown below. This credential is comprised of all three of your credentals combined into one line of code. You’ll use the CLOUDINARY_URL to authenticate and get authorization to upload. You can easily copy the CLOUDINARY_URL into your clipboard by clicking on the clipboard icon.

![cloudinary url](https://res.cloudinary.com/jen-brissman/image/upload/v1702021754/cld-url.png)

For Mac (bash or zsh):

```bash
export CLOUDINARY_URL=cloudinary://API_KEY:API_SECRET@cloud_name
```

For Windows (powershell):
```bash
set CLOUDINARY_URL=cloudinary://API_KEY:API_SECRET@cloud_name
```

### Config

After setting the environment variable, call `config` to make cloud_name, api_secret, and api_key available to the CLI.
Running the `config` command will out put these values separately in the terminal.  If you ever accidentally reveal the API_SECRET, you can reset in the console.

```bash
export CLOUDINARY_URL=cloudinary://API_KEY:API_SECRET@cloud_name
cld config
    cloud_name:	cloud_name
    api_key:	API_KEY
    api_secret:	***************************
```

### Multiple Configs

You setup and reference multiple configs by name.  If you set up using this command, you'll need to reference the Cloudinary config by name when you issue a command.

Setup by name (usage)

```bash
cld config --from_url <CLOUDINARY_URL from console>
cld -C <cloud_name> <command>
```

Setup by name (example)

```bash
cld config --from_url cloudinary://API_KEY:API_SECRET@cloud_name
cld -C cloud_name config
```

---

## Helper Tools

You can get usage help on any command

```bash
cld --help # lists available commands
cld <command> --help #general help

example:
cld search --help 	# Search API usage

cld <API> #Lists functions of that API

example:
cld admin     # Admin API functions
cld uploader    # Upload API functions

```
---

## Uploading

### Widgets

You can easily access the HTML for Cloudinary widgets via the CLI, which you can then copy/paste and embed on your site.

Upload Widget:
```bash
cld make upload widget
```

Media Library widget:
```bash
cld make media library widget
```

Product Gallery widget:
```bash
cld make product_gallery widget
```

Video Player widget:
```bash
cld make video_player widget
```


### Uploading Files by Type

Upload (usage)

```bash
cld upload file [options]  
```

Upload (examples)
```bash
cld upload sample.jpg resource_type=image
cld upload sample.jpg resource_type=auto
cld upload sample.jpg
cld upload dog.mp4 resource_type=video
cld upload dog.mp4 resource_type=auto
cld upload dog.mp4
cld upload data.json resource_type=raw
cld upload data.json resource_type=auto
cld upload data.json
```

Resource type can be found in the URL after the cloud name.

[res.cloudinary.com/demo/**image**/upload/sample.jpg](https://res.cloudinary.com/demo/image/upload/sample.jpg)

[res.cloudinary.com/demo/**video**/upload/dog.mp4](https://res.cloudinary.com/demo/video/upload/dog.mp4)

### Set Public ID during Upload

Explicitly Set the public id.  The uploaded asset public id will be **sample**.

```bash
cld upload sample.jpg public_id=sample_id
```

Assign the Asset Filename.  The uploaded public id will be **sample**.

```bash
cld upload sample.jpg use_filename=true unique_filename=false
```

If you remove the options to make the unique filename false or set it to true, the filename will be appended with `_xxxxxx` representing 6 random characters.  The uploaded public id will be `sample_xxxxxx`.

```bash
cld upload sample.jpg use_filename=true 
```

If you don't specify a public id or filename the asset public id will be a string of 32 random characters.

```bash
cld upload sample.jpg
```
### Folders

You can create folders (directories) on the fly by adding them to your public id.  The following examples create assets with the same public id.

```bash
$ cld upload sample.jpg public_id=my_folder/my_sub_folder/my_name
```

You can also specify folders as an option.

```bash
cld upload sample.jpg folder=my_folder/my_sub_folder/ public_id=my_name
```

### Source Options

#### HTTP or HTTPS URL

```bash
cld upload http://www.example.com/sample.jpg
```

#### Private Storage URL (e.g. Amazon S3 or Google Cloud Storage) 

```bash
cld upload s3://my-bucket/my-path/example.mp4
```

#### Base-64 Data URI 

```bash
cld upload 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg=='
```

#### FTP URL

```bash
cld upload ftp://user1:mypass@ftp.example.com/sample.jpg
```
### Fetching Remote Assets

#### Fetch

Fetch is a "delivery" **type**.  Other deliver types include **upload**, **private** and **authenticated**.  You find these after the resource type in the URL.  

[res.cloudinary.com/demo/image/**upload**/sample.jpg](https://res.cloudinary.com/demo/image/upload/sample.jpg]

With Fetch you specify the full URL of the remote image and that URL can be found in the Cloudinary URL.  Fetch is only for images.

```bash
cld url -t fetch 'http://upload.wikimedia.org/wikipedia/commons/b/b1/Carnegiea_gigantea_(3).jpg'
```

The `cld url` command creates a URL string.  The string created by the command above is show below. 

res.cloudinary.com/<cloud name>/image/**fetch**/http://upload.wikimedia.org/wikipedia/commons/b/b1/Carnegiea_gigantea_%283%29.jpg

You may need to change your settings because **Fetched URL** is disabled by default.

#### Auto-Upload

Create a mapping between a Cloudinary folder and a remote URI (path).  Auto-Upload will work for all asset types.

```bash
cld create_upload_mapping \
remote_media \
template=https://upload.wikimedia.org/wikipedia/
```

Create a URL to request an asset using the mapping to upload the asset to Cloudinary in the mapped folder.

```open
cld url --open remote_media/commons/b/b4/Apollo_7_Florida.jpg
```
## Cloudinary URLs

We'll be using the URL helper to create URLs.  URL requests are handled by Cloudinary and cached on the CDN and then a response is returned.

We can create a URL string 

Usage
```bash
cld url [OPTIONS] PUBLIC_ID [TRANSFORMATION]
```

If you want to make a request using the URL, add the `--open` option

```bash
cld url --open [OTIONS] PUBLIC_ID [TRANSFORMATION]
```


## Managing Uploaded Content

Admin functions

### List all images 

(10 by default).

```bash
cld admin resources
```
For up to 500 in the list.

```bash
cld admin resources max_results=500
```

List all images with prefix.

```bash
cld resources type=upload prefix=sample
```
### Rename

Usage

```bash
cld upload rename old_name new_name
```
Example

```bash
cld upload rename sample old-sample
```

### Remove with Destroy and Delete

#### Destroy

Remove one asset at a time using pubic id.

```bash
cld uploader destroy sample
```

### Delete

Remove multiple assets using public id.

```bash
cld admin delete_resources image1,image2
```

Remove multiple assets using prefix.

```bash
cld admin delete_resources_by_prefix sunday
```

### Tag on upload

```bash
cld upload sample.jpg tags=animal
```

### Tag after upload

```bash
cld add_tag animal public_ids=sample
```

### Remove a single tag by name

```bash
cld remove_tag animal sample
```

### Remove all tags

```bash
cld remove_all_tags sample  
```

### Remove assets from CDN

Use the `invalidate` option.

```bash
cld destroy sample invalidate=true
```

---
#### Sync

Synchronize a local directory to the cloud using `push` and `pull` command.  `push` copies the local directory up to the cloud and `pull` downloads a cloud directory to the local file system.

Usage

```bash
# upload
cld sync --push <local directory> <cloud directory>
# download
cld sync --pull <local directory> <cloud directory>
```
Examples

```bash
cld sync --push ~/Pictures/images test-images
```

```bash
cld sync --pull ~/Pictures/myimages test-images
```

---
## Transformations

 Transformations can be provided as comma separated lists of options as they appear in the URL or as key/value assignments.

Create URLs with Transformations including cropping (height, width, crop), formatting (fetch_format) and compressing (quality).  

### Crop

The code below will set the width to 300, height to 200 and crop using scale by default.  The resource type is set to video.

This example provides the transformation as it would appear in the URL and the resource type as an option.

```bash
cld url dog.mp4 w_300,h_200,c_crop -rt video
```

You'll see this URL:
[res.cloudinary.com/demo/**video**/upload/**w_300,h_200,c_crop**/dog.mp4](res.cl]oudinary.com/demo/video/upload/w_300,h_200,c_crop/dog.mp4)

### Format

This example provides the transformation as a key/value assignment.  The `f_` is a shorthand for format.

```bash
cld url toshi.jpg f_auto
```

You'll see this URL:
[res.cloudinary.com/demo/image/upload/**f_auto**/toshi.jpg](http://res.cloudinary.com/demo/image/upload/f_auto/toshi.jpg)

### Quality

This example provides the transformation as a key/value assignment

```bash
cld url robot.jpg q_auto
```
You'll see this URL:
res.cloudinary.com/demo/image/upload/**q_auto**/robot.jpg

### Rounding

Use `r_<value>` to create rounding similar to the CSS `border-radius`.  A value of `max` will create a circle.

```bash
cld url face_left.png w_200,h_200,g_face,r_max,c_thumb
```
You'll see this URL:

[res.cloudinary.com/demo/image/upload/w_200,h_200,c_thumb,g_face,**r_max**/face_left.png](https://res.cloudinary.com/demo/image/upload/w_200,h_200,c_thumb,g_face,r_max/face_left.png
)

### Borders

Use `bo_<size in pixels>_<style>_<color>` to create borders similar to the CSS `border` command.

```bash
cld url sample.jpg bo_5px_solid_red
```

You'll see this URL:
[res.cloudinary.com/demo/image/upload/**bo_5px_solid_red**/sample.jpg](https://res.cloudinary.com/demo/image/upload/bo_5px_solid_red/sample.jpg)

### Background Color

Use `b_<option>` to add a background color.  Options include `auto` which chooses the image predominant color, or `auto:border_contrast`, or `auto:predominant_contrast`. 

```bash
cld url sample.jpg h_300,w_300,c_pad,b_auto
```

You'll see this URL:

[res.cloudinary.com/demo/image/upload/h_300,w_300,c_pad,**b_auto**/sample.jpg](https://res.cloudinary.com/demo/image/upload/h_300,w_300,c_pad,b_auto/sample.jpg)

### Effects and Filters

The `e_` is for effect.  You can see the [documentation](https://cloudinary.com/documentation/image_transformations#applying_image_effects_and_filters) on effects and filters to see all the options. 

### Outline Effect 

In the transformation below the balloon has an orange outline.

```bash
cld url -o -t upload -o balloon.png h_200/e_outline,co_orange/
```
You'll see this URL. Notice that the outline has a width and blur.  The values are defaulted in but you can add your own. The width is 15px and the blur is 200.

[res.cloudinary.com/demo/image/upload/h_200,c_scale/**e_outline:15:200,co_orange**/balloon.png](https://res.cloudinary.com/demo/image/upload/h_200,c_scale/e_outline:15:200,co_orange/balloon.png)

### Improve Effect

The improve effect is one of many that apply visual enhancements: `improve`, `gamma`,`auto_brightness`, `auto_contrast`, `auto_color`, `fill_light`, `vibrance`, `contrast`, `viesus_correct`.

```bash
cld url -o sample.jpg e_improve:outdoor
```

You'll see this URL

[res.cloudinary.com/demo/image/upload/**e_improve:outdoor**/sample.jpg
](https://res.cloudinary.com/demo/image/upload/e_improve:outdoor/sample.jpg)

### Art Filters
Try out some of the art filters: `al_dente`, `athena`, `artistic`, `audrey`, `artistic` `filteraudrey`, `daguerre` and more.

```bash
cld url -o horses.jpg e_art:quartz
```
You'll see a URL like this

[res.cloudinary.com/demo/image/upload/**e_art:quartz**/horses.jpg](https://res.cloudinary.com/demo/image/upload/e_art:quartz/horses.jpg)

### Overlay

You render an image, video, or text on top of another asset. These have many uses including watermarking.

#### Image over Image

This positions the icon on top of the other image using a compass position of `north_east` which is the upper right. 

```bash
cld url \
sample.jpg \
l_cloudinary_icon,g_north_east
```

You'll see a URL like this

[res.cloudinary.com/demo/image/upload/l_cloudinary_icon,**g_north_east**/sample.jpg
](https://res.cloudinary.com/demo/image/upload/l_cloudinary_icon,g_north_east/sample.jpg
)

#### Text over Image
When specifying text you need to use it like this `font_family_<size in pixels>_<style>:<text>`.  This example using `x`, `y` positioning relative to the center of gravity, which in this case is the `north_east`. The text is therefore offset down and left 15 pixels from the upper right.  If gravity is not included the default is the center of the asset.  The color can be specified as a hex value like this example or with a color name.

```bash
cld url \
-t upload \
-o \
sample.jpg \
c_scale,w_500/co_rgb:ffff00,g_north_east,l_text:Times_90_bold:Bees!,x_15,y_15 
```

You'll see a URL like this  

[res.cloudinary.com/demo/image/upload/c_scale,w_500/**co_rgb:ffff00,g_north_east,l_text:Times_90_bold: Bees!,x_15,y_15**/sample.jpg](https://res.cloudinary.com/demo/image/upload/c_scale,w_500/co_rgb:ffff00,g_north_east,l_text:Times_90_bold:%20Bees!,x_15,y_15/sample.jpg)

---

## Presets

Preset are a named set of instructions to be carried out when an asset is uploaded.  It's often used to apply similar crop instructions to any asset that uses it.

### Create a preset

The preset below is named **mobile_profile_photo** and instructs that the asset be cropped using a 640x640 limit crop. It also assigns a tag with the value **mobile_upload** to the asset.

```bash
cld admin create_upload_preset name=mobile_profile_photo width=640 height=640 crop=limit tags=mobile_upload
```

### Use a preset during upload

```bash
cld upload santa.jpg upload_preset=mobile_profile_photo
```

---

## Named Transformations

### Create a named transformation

Any transformation string can be stored by name.  This can make cleaner URLs and be used for templates. The transformation is named **small_fill2** in the example below.  The `gravite=face` option will attempt center the image on face. 

```bash
cld admin create_transformation small_fill2 width=150 height=100 crop=fill gravity=face
```

### Use a named transformation

```bash
cld url sample t_small_fill2
```

[https://res.cloudinary.com/picturecloud7/image/upload/t_small_fill2/sample](https://res.cloudinary.com/picturecloud7/image/upload/t_small_fill2/sample)

---

## Backups

[Enable and Configure backups](https://cloudinary.com/documentation/backups_and_version_management#enabling_automatic_backup) in the DAM. 

You can use the CLI ensure a specific asset is backed up.

```bash
cld upload sample.jpg backup=true
```

---

## Resources

[Cloudinary Academy](training.cloudinary.com)

[Introduction to Cloudinary's CLI Course (Free!)](https://training.cloudinary.com/learn/course/introduction-to-cloudinarys-cli-one-hour-course/lessons/setting-up-your-environment-0820)

[Cloudinary Documentation](https://cloudinary.com/documentation) 

[Cloudinary Academy GitHub Respositories](https://github.com/cloudinary-training)

[Cloudinary GitHub Repository](https://github.com/cloudinary)

[Support Request](https://support.cloudinary.com/hc/en-us/requests/new)

[Support Email support@cloudinary.com](mailto:support@cloudinary.com)


## Asset Credits

- tent.jpg - [image from Pexels by Sagui Andrea](https://www.pexels.com/photo/blue-dome-tent-near-mountain-618848/)
- hiker.jpg - [image from Pexels by Ali Kazal](https://www.pexels.com/photo/man-in-gray-knit-cap-and-gray-zip-up-jacket-smiling-10772325/)
- hiking.jpg - [image from Pexels by Sagui Andrea](https://www.pexels.com/photo/blue-dome-tent-near-mountain-618848/)
- dolomites.jpg - [photo from Pexels by Thomas Vitali](https://www.pexels.com/photo/a-rock-mountain-surrounded-by-snow-covered-ground-3039336/)
- pnw.jpg - [image from Pexels by PNW Production](https://www.pexels.com/photo/woman-in-white-bubble-jacket-7625303/)
- coffee-with-a-view.jpg - [image from Pexels by Andy Vu](https://www.pexels.com/photo/man-in-brown-jacket-and-brown-hat-standing-on-rock-near-lake-3217911/)
- backpack.mp4 - [video from Pexels by Mikita Yo](https://www.pexels.com/video/a-man-standing-still-on-a-cliff-edge-8644064/)
- water-bottle.mp4 - [video from Pexels by Taryn Elliott](https://www.pexels.com/video/mountains-hiking-mountain-hiking-nature-lover-4909308/)
- kids-hiking.mp4 - [video from Pexels by RDNE Stock Project](https://www.pexels.com/video/two-boys-walking-on-a-park-8083560/)
- waterfall.mp4 - [video from Pexels by Beckett Johnson](https://www.pexels.com/video/footage-of-waterfalls-5022215/)
- crossing.mp4 - [video from Pexels by Yaroslav Shuraev](https://www.pexels.com/video/a-couple-crossing-the-river-8968040/)


