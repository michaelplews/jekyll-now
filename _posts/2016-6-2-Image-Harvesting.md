---
layout: post
title: Webscraping for Vector Graphics
---

My first blog post ends up being not about research at all, but hopefully it'll be interesting none the less. I've always said that the majority of what you learn in your graduate career is nothing to do with the subject you're studying. In my case, I've had to learn a lot of data processing and note taking about a large number of chemical reactions. I've since taken a liking to Python, and all that the language has to offer. iPython Notebook especially has helped me keep track of samples and plot data in a format almost like a digital notebook.

So when I decided on a graphics project for an upcoming design competition in the chemistry department, I ran into a problem. I wanted to have an image rather like this one but instead of Lincoln I wanted my university logo, and instead of words I wanted molecules (the kind drawn using ChemDraw) as shown below.

![Caffeine]({{ site.baseurl }}/images/caffeine_vector.png)

This posed a few hurdles:

 - I don't have enough time to download the number of images I'll need.
 - I'll want the images in vector (.svg) format so that means they will NEED to come from ChemDraw.
 - I'll need the images to be allowed to be reproduced through Creative Commons.

After a quick Google search the answer almost fell in my lap. All the results were from Wikipedia. It makes sense, right? Every page on a chemical usually has an image of the chemical structure, and almost all of them are made into .svg files to allow for reuse.

Another quick Google search lead me to the Wikipedia package for Python. So without further ado, here is how I downloaded hundreds of vector images from Wikipedia with a few lines of code.

## The Code

### Requirements:
```bash
pip install wikipedia
```

### The Python:
```python
import wikipedia
```

I thought to start by introducing a few test pages to test the code, so I chose these below.

```python
pages = [
	"taxol", "sucrose", "oxaliplatin", "cholesterol", "Leuprorelin"
]
```

I ran a search for all these pages, skipping any errors that came up with `try: except:` commands. Seeing as this is just test code, I only had the result print the name.

```python
for pages in pages:
    try:
        search = wikipedia.search(pages)               #Returns the wikipedia search as a list of actual wiki pages
        image_list = wikipedia.page(search[0]).images  #Takes the first page found by the line above and finds all the images in that page
    except:
        pass
    else:
        for image in image_list:
            if image[-3:] in ("SVG", "svg"):           #Make sure the images are only .svg format
                print (image)
```
Output:
```
https://upload.wikimedia.org/wikipedia/en/9/99/Question_book-new.svg
https://upload.wikimedia.org/wikipedia/commons/0/0c/TaxolNomenClature.svg
https://upload.wikimedia.org/wikipedia/commons/2/2e/Ambox_contradict.svg
https://upload.wikimedia.org/wikipedia/en/f/fb/Yes_check.svg
https://upload.wikimedia.org/wikipedia/commons/a/a2/X_mark.svg
https://upload.wikimedia.org/wikipedia/commons/5/59/Taxol.svg
https://upload.wikimedia.org/wikipedia/commons/6/6b/Taxol_number.svg
https://upload.wikimedia.org/wikipedia/commons/5/58/TaxolStereochemistry.svg
https://upload.wikimedia.org/wikipedia/commons/6/6f/NFPA_704.svg
https://upload.wikimedia.org/wikipedia/en/f/fb/Yes_check.svg
https://upload.wikimedia.org/wikipedia/en/4/4a/Commons-logo.svg
https://upload.wikimedia.org/wikipedia/commons/a/a2/X_mark.svg
https://upload.wikimedia.org/wikipedia/commons/a/ae/World_raw_sugar_prices_since_1960.svg
https://upload.wikimedia.org/wikipedia/commons/1/1a/Saccharose2.svg
https://upload.wikimedia.org/wikipedia/en/4/48/Folder_Hexagonal_Icon.svg
https://upload.wikimedia.org/wikipedia/en/f/fb/Yes_check.svg
https://upload.wikimedia.org/wikipedia/commons/a/a2/X_mark.svg
https://upload.wikimedia.org/wikipedia/commons/1/13/Steroidogenesis.svg
https://upload.wikimedia.org/wikipedia/en/f/fb/Yes_check.svg
https://upload.wikimedia.org/wikipedia/commons/a/a2/X_mark.svg
https://upload.wikimedia.org/wikipedia/commons/9/9a/Cholesterol.svg
https://upload.wikimedia.org/wikipedia/en/f/fb/Yes_check.svg
https://upload.wikimedia.org/wikipedia/commons/a/a2/X_mark.svg
https://upload.wikimedia.org/wikipedia/commons/d/dc/Leuprorelin.svg
```

From the list above you can see that we might require some additional filtering to make sure we only get images of interest. For example, 'Folder_Hexagonal_Icon.svg', 'World_raw_sugar_prices_since_1960.svg' and 'Yes_check.svg' probably aren't what we're looking for.

```python
for pages in pages:
    try:
        search = wikipedia.search(pages)               
        image_list = wikipedia.page(search[0]).images
    except:
        pass
    else:
        for image in image_list:
            if image[-3:] in ("SVG", "svg") and 'commons' in image and '_mark' not in image and 'Flag_' not in image:
                #The above line has some additional filters
                print (image)
```
Output:
```
https://upload.wikimedia.org/wikipedia/commons/0/0c/TaxolNomenClature.svg
https://upload.wikimedia.org/wikipedia/commons/5/59/Taxol.svg
https://upload.wikimedia.org/wikipedia/commons/2/2e/Ambox_contradict.svg
https://upload.wikimedia.org/wikipedia/commons/6/6b/Taxol_number.svg
https://upload.wikimedia.org/wikipedia/commons/5/58/TaxolStereochemistry.svg
https://upload.wikimedia.org/wikipedia/commons/a/ae/World_raw_sugar_prices_since_1960.svg
https://upload.wikimedia.org/wikipedia/commons/1/1a/Saccharose2.svg
https://upload.wikimedia.org/wikipedia/commons/6/6f/NFPA_704.svg
https://upload.wikimedia.org/wikipedia/commons/1/13/Steroidogenesis.svg
https://upload.wikimedia.org/wikipedia/commons/9/9a/Cholesterol.svg
https://upload.wikimedia.org/wikipedia/commons/d/dc/Leuprorelin.svg
```

This is much more what we're looking for, now to apply it to a much larger list. After a quick google, I found a website that lists [all anti-cancer drugs recognized by the National Institute of Health](http://www.cancer.gov/about-cancer/treatment/drugs). Anti-cancer drugs can be large and complex, and this is exactly the kind of images I'm looking for.

I copy & pasted the webpage contents into a .txt file and tidied it up a bit. Next I'll import the lines of the .txt file into a list.

```python
pages = [line.rstrip('\n') for line in open('./anti_cancer_drugs_NIH.txt')]
```

Now let's put everything together including the downloading the files with urllib.request and run the script. This may take a while!

```python


import urllib.request
count = 0
for pages in pages:
    try:
        search = wikipedia.search(pages)
        image_list = wikipedia.page(search[0]).images
    except:
        pass
    else:
        for image in image_list:
            if image[-3:] in ("SVG", "svg") and 'commons' in image and '_mark' not in image and 'Flag_' not in image:
                filename = image.split('/')[-1]
                urllib.request.urlretrieve(image, "./images/" + filename)
                #The above line will save the file in the './images' folder in whatever directory you're working in
                count+=1
print (str(count) + " svg's were downloaded!")
```
Output:
```
255 svg's were downloaded!
```

255 vector images will be plenty for the graphics project. Success!

After some Inkscape work, here is the final product:

![UIC 2016 T-Shirt Design]({{ site.baseurl }}/images/UIC_Tshirt.png)

### Optional Code

The rest of this code is purely additional due to the type of project I'm doing. I decided to provide a list of chemical structures in the graphic to the organisers just so they have it at hand if needed. Every time I used a .svg file in the image, I dropped it into the 'used' folder so I didn't use it again. The following code takes all the filenames in the used folder and writed them to a .txt file and returns a count so i know how many vectors make up my larger image.

```python
#Optional Code
from os import listdir
from os.path import isfile, join

onlyfiles = [f for f in listdir("./images/used/") if isfile(join("./images/used/", f))]
f = open("./List_of_used_images.txt", "w")
count = 0
for file in onlyfiles:
    f.write(file + "\n")
    count+=1
print (str(count) + " svg's were used in the image!")
```
Output:
```
138 svg's were used in the image!
