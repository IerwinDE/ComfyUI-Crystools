# ComfyUI-Crystools [![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fcrystian%2FComfyUI-Crystools&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com) [![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://paypal.me/crystian77)

**_A powerful set of tools for your belt when you work with ComfyUI_**

You can see the metadata and compare between two images, compare between two JSONs, show any value to console/display, pipes, and more!  
This provides a better nodes to load images, previews, etc, and see "hidden" data, but without load a new workflow.

![Show metadata](./docs/jake.gif)

# Table of contents

- [Metadata](#metadata)
- [Debugger](#debugger)
- [Image](#image)
- [Pipe](#pipe)
- [Utils](#utils)
- [Primitives](#primitives)
- [List](#list)
- [Switch](#switch)
- Others: [About](#about), [To do](#to-do), [Installation](#installation), [Use](#use)



## Metadata

### Node: Metadata extractor

This node is used to extract the metadata from the image and handle it as a JSON as source for other nodes.  
You can see **all information**, even metadata from others sources (like photoshop, see sample).

The input comes from the [load image with metadata](#node-load-image-with-metadata) or [preview from image](#node-preview-from-image) nodes (an others in the future).

![Metadata extractor](./docs/metadata-extractor.png)

><details>
>  <summary>Other metadata sample (photoshop)</summary>
> 
> With metadata from Photoshop
![Metadata extractor](./docs/metadata-extractor-photoshop.png)
></details>

><details>
>  <summary><i>Parameters</i></summary>
>
> - input: 
>   - metadata_raw: The metadata raw from the image or preview node
> - Output:
>   - prompt: The prompt used to produce the image.
>   - workflow: The workflow used to produce the image (all information about nodes, values, etc).
>   - file info: The file info of the image/metadata (resolution, size, etc) as human readable.
>   - raw to JSON: The entire metadata raw but formatted/readable.
>   - raw to property: The entire metadata raw as "properties" format.
>   - raw to csv: The entire metadata raw as "csv" format.
></details>

### Node: Metadata comparator

This node is so useful to compare two metadata and see the differences (**the main reason why I created this extension!**)

You can compare 3 inputs: "Prompt", "Workflow" and "Fileinfo"

There are tree potential "outputs": `values_changed`, `dictionary_item_added`, `dictionary_item_removed` (in this order of priority).

![Metadata extractor](./docs/metadata-comparator.png)

**Notes:**  
- I use [DeepDiff](https://pypi.org/project/deepdiff) for that, for more info check the link.  
- If you want to compare two JSONs, you can use the [JSON comparator](#node-JSON-comparator) node.


><details>
>  <summary><i>Parameters</i></summary>
>
> - options:
>   - what: What to compare, you can choose between "Prompt", "Workflow" and "Fileinfo"  
> - input: 
>   - metadata_raw_old: The metadata raw to start compare
>   - metadata_raw_new: The metadata raw to compare
> - Output:
>   - diff: This is the same output you can see in the display of the node, you can use it on others nodes.
></details>



## Debugger

### Node: Show Metadata

With this node, you will be able to see the JSON produced from your entire prompt and workflow so that you can really know all the values (and more) of your prompt quickly without the need to open the file (PNG or JSON).

![Show metadata](./docs/debugger-show-metadata.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Options:
>   - Active: Enable/disable the node  
>   - Parsed: Show the parsed JSON or plain text  
>   - What: Show the prompt or workflow (prompt are values to produce the image, and workflow is the entire workflow of ComfyUI)
></details>

### Node: Show any

You can see on the console or display any text or data from the nodes. Connect it to what you want to inspect, and you will see it.

![Show any](./docs/debugger-show-any.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - any_value: Any value to show, can be a string, number, etc.
> - Options:
>   - Console: Enable/disable write to console  
>   - Display: Enable/disable write on this node  
>   - Prefix: Prefix to console
></details>

### Node: Show any to JSON

As same the previous one, but it formatted the value to JSON (only display).

![Show any](./docs/debugger-show-json.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - any_value: Any value to try to convert to JSON
> - Output:
>   - string: The same string is shown on display
></details>


## Image

### Node: Load image with metadata
This node is the same as the default one, but it adds three features: Prompt, Metadata, and supports **subfolders** of the "input" folder.

![Load image with metadata](./docs/image-load.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - image: Read the images from the input folder (and subfolder) (you can drop the image here, or even paste an image from clipboard)
> - Output:
>   - Image/Mask: The same as the default node  
>   - Prompt: The prompt used to produce the image (not the workflow)  
>   - Metadata RAW: The metadata raw of the image (full workflow) as string
></details>

**Note:** The subfolders support inspired on: [comfyui-imagesubfolders](https://github.com/catscandrive/comfyui-imagesubfolders)

### Node: Preview from image

This node is used to preview the image with the **current prompt** and additional features.  

![Preview from image](./docs/image-preview.png)

**Feature:** It supports cache (shows as "CACHED") (not permanent yet!), so you can disconnect the node and still see the image and data, so you can use it to compare with others!

![Preview from image](./docs/image-preview-diff.png)

As you can see the seed, steps, and cfg were changed

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - image: Any kind of image link
> - Output:
>   - Metadata RAW: The metadata raw of the image and full workflow.  
>     - You can use it to **compare with others** (see [metadata comparator](#node-metadata-comparator))
>     - The file info like filename, resolution, datetime and size with **the current prompt, not the original one!** (see important note)
></details>

> **Important:**
> - If you want to read the metadata of the image, you need to use the [load image with metadata](#node-load-image-with-metadata) and use the output "metadata RAW" not image link.
> - To do a preview it is necessary save it first on temporal folder, and the data shown is from the temporal image, **not the original one** even **the prompt!**


### Node: Preview from metadata

This node is used to preview the image from the metadata and shows additional data (all around this one).  
It supports same features as [preview from image](#node-preview-from-image) (cache, metadata raw, etc.). But the important difference is you see **real data from the image** (not the temporal one either current prompt).
 
![Preview from metadata](./docs/image-preview-metadata.png)

### Node: Show resolution

This node is used to show the resolution of an image.

> Can be used with any image link.

![Show resolution](./docs/image-resolution.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - image: Any kind of image link
> - Output:
>   - Width: The width of the image  
>   - Height: The height of the image
></details>



## Pipe

### Nodes: Pipe to/edit any, Pipe from any

This powerful set of nodes is used to better organize your pipes.  

The "Pipe to/edit any" node is used to encapsulate multiple links into a single one. It includes support for editing, easily adding the modified content back to the same pipe number.

The "Pipe from any" node is used to extract the content of a pipe.  
 
Typical example:

![Pipes](./docs/pipe-0.png)

With pipes:

![Pipes](./docs/pipe-1.png)

Editing pipes:

![Pipes](./docs/pipe-2.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - CPipeAny: This is the type of this pipe you can use it to edit (see the sample)
>   - any_*: 6 possible inputs to use
> - Output:
>   - CPipeAny: You can continue the pipe with this output, you can use it to bifurcate the pipe (see the sample)
></details>

>**Important:**
> - Please note that it supports "any," meaning it does not validate (not yet!) the correspondence of input nodes with the output ones. When creating the link, it is recommended to link consciously number by number.
> - "RecursionError", It's crucial to note that the flow of links **must be in the same direction**, and they cannot be mixed with other flows that use the result of this one. Otherwise, this may lead to recursion and block the server (you need to restart it!)


><details>
>  <summary><i>Bad example with "RecursionError: maximum recursion depth exceeded"</i></summary>
>
> If you see on your console something like this, you need to check your pipes. That is bad sample of pipes, you can't mix the flows.
![Pipes](./docs/pipe-3.png)
></details>



## Utils

Some useful nodes to use in your workflow.

### Node: JSON comparator

This node is so useful to compare two JSONs and see the differences.

![JSON comparator](./docs/utils-json-comparator.png)


><details>
>  <summary><i>Parameters</i></summary>
>
> - input: 
>   - json_old: The first json to start compare
>   - json_new: The json to compare
> - Output:
>   - diff: A new JSON with the differences
></details>


**Notes:**  
As you can see, it is the same as the [metadata comparator](#node-metadata-comparator) but with JSONs.  
The other is intentionally simple to compare two images metadata, this is more generic.  
The main difference is that you can compare any JSON, not only metadata.

### Node: Stats system

This node is used to show the stats of the system (RAM, VRAM and Space).  
It **should** connect as a pipe.

![JSON comparator](./docs/utils-stats.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - input: 
>   - latent: The latent to use to measure the stats
> - Output:
>   - latent: Return the same latent to continue the pipe
></details>

**Notes:** The original is in [WAS](https://github.com/WASasquatch/was-node-suite-comfyui), I only show it on the display.


## Primitives

### Nodes: Primitive boolean, Primitive integer, Primitive float, Primitive string, Primitive string multiline

A set of nodes with primitive values to use in your prompts.

![Primitives](./docs/primitives.png)


## List
A set of nodes with list of values (any or strings/texts) for any propose (news nodes to use it coming soon!).

> **Important:** You can use with others nodes like "Show any" to see the values of the list

### Node: List of strings

**Feature:** You can concatenate them.

![Lists](./docs/list-string.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - string_*: 8 possible inputs to use
>   - delimiter: Use to concatenate the values on output
> - Output:
>   - concatenated: A string with all values concatenated
>   - list_string: The list of strings (only with values)
></details>

### Node: List of any

You can concatenate any value (it will try to convert to string and show the value), so util to see several values at the same time.

![Lists](./docs/list-any.png)

><details>
>  <summary><i>Parameters</i></summary>
>
> - Input:
>   - any_*: 8 possible inputs to use
> - Output:
>   - list_any: The list of any elements (only with values)
></details>



## Switch
A set of nodes to switch between flows.  

All switches are boolean, you can switch between flows by simply changing the value of the switch.  
You have predefined switches (string, latent, image, conditioning) but you can use "Switch any" for any value/type.

![Switches](./docs/switches.png)



## About

**Notes from the author:**
- This is my first project in python ¯\_(ツ)_/¯ (PR are welcome!)
- I'm software engineer but in other languages (web technologies)
- My instagram is: https://www.instagram.com/crystian.ia I'll publish my works on it, consider follow me for news! :)
- I'm not a native english speaker, so sorry for my english :P

## To do
- [ ] Add support for others image formats (jpg, gif, etc.)
- [ ] Several unit tests
- [ ] Add permanent cache for preview/metadata image (to survive to F5! or restart the server)



## Installation

### Install from GitHub
1. Install [ComfyUi](https://github.com/comfyanonymous/ComfyUI).
2. Clone this repo into `custom_modules`:
    ```
    cd ComfyUI/custom_nodes
    git clone https://github.com/crystian/ComfyUI-Crystools.git
    cd ComfyUI-Crystools
    pip install -r requirements.txt
    ```
3. Start up ComfyUI.

### Install from manager

Search for `crystools` in the [manager](https://github.com/ltdrdata/ComfyUI-Manager.git) and install it.



## Use

You can use it as any other node, just using the menu in the category `crystools` or double click on the canvas (I recommended to use the "oo" to fast filter), all nodes were posfixing with `[Crystools]`.

![Menu](./docs/menu.png)
![shortcut](./docs/shortcut.png)

If for some reason you need to see the logs, you can define the environment variable `CRYSTOOLS_LOGLEVEL` and set the [value](https://docs.python.org/es/3/howto/logging.html).

