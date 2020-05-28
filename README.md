# Cheatsheet: Introducton to Cloudinary CLI

## Setup Environment

### Install Python

- Python 3.6 or greater
- Download: https://www.python.org/downloads/
- Mac : brew install recommended https://installpython3.com/mac/

### Install Cloudinary

**Initial install:**

```bash
pip3 install cloudinary-cli
```

**Update:**

```bash
 pip3 install cloudinary-cli --upgrade
 ```

### Cloudinary Credentials

### Setup Environemnt Variables

Navigate to:  [https://cloudinary.com/console](https://cloudinary.com/console)

Sign up for free account or login to find credentials. 

Locate your cloudname.

![cloud name](https://res.cloudinary.com/cloudinary-training/image/upload/v1590703923/book/get-cloud-name.png)

Locate your API_KEY and API_SECRET.  Don't reveal these publicly.

![key and secret](https://res.cloudinary.com/cloudinary-training/image/upload/v1590704088/book/key-secret.png)

Locate the ClOUDINARY_URL and click the link to copy it into your buffer.

![cloudinary url](https://res.cloudinary.com/cloudinary-training/image/upload/v1590704298/book/cld-url.png)

Add the Cloudinary URL to your environment variables.

For Mac (bash or zsh):

```bash
export CLOUDINARY_URL=cloudinary://API_KEY:API_SECRET@cloud_name
```

For Windows (powershell):
```powershell
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
    api_secret:	***************CRET
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
## Help

You can get usage help on any command

```bash
cld <command> --help
```

## Uploading

The CLI will automatically detect your image type but you can specify it.

### Upload Widget

The Cloudinary Media Library (console online) uses an Upload Widget that you can add to your own HTML files. The CLI can write the code for you to copy/paste.  You can make other widgets as well like **video player**.

```bash
cld make upload widget
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

res.cloudinary.com/demo/**image**/upload/sample.jpg

res.cloudinary.com/demo/**video**/upload/dog.mp4

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

res.cloudinary.com/demo/image/**upload**/sample.jpg

With Fetch you specify the full URL of the remote image and that URL can be found in the Cloudinary URL.  Fetch is only for images.

```bash
cld url -t fetch 'http://upload.wikimedia.org/wikipedia/commons/b/b1/Carnegiea_gigantea_(3).jpg'
```

The `cld url` command creates a URL string.  The string created by the command above is show below. 

res.cloudinary.com/<cloud name>/image/**fetch**/http://upload.wikimedia.org/wikipedia/commons/b/b1/Carnegiea_gigantea_%283%29.jpg


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


## Managing Uploded Content

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


## Transformations: Optimziation

 Transformations can be provided as comma separated lists of options as they appear in the URL or as key/value assignments.

Create URLs with Transformations including cropping (height, width, crop), formatting (fetch_format) and compressing (quality).  

### Crop

The code below will set the width to 300, height to 200 and crop using scale by default.  The resource type is set to video.

This example provides the transformation as it would appear in the URL and the resource type as an option.

```bash
cld url dog.mp4 w_300,h_200,c_crop -rt video
```

You'll see this URL:
res.cloudinary.com/demo/**video**/upload/**w_300,h_200,c_crop**/dog.mp4

### Format

This example provides the transformation as a key/value assignment.

```bash
cld url toshi.jpg fetch_format=auto
```

You'll see this URL:
res.cloudinary.com/demo/image/upload/**f_auto**/toshi.jpg

### Quality

This example proivdes the transformation as a key/value assignment

```bash
cld url robot.jpg quality=auto
```
You'll see this URL:
res.cloudinary.com/demo/image/upload/**q_auto**/robot.jpg




## Transformations: Aesthetics

## Presets

## Backups



