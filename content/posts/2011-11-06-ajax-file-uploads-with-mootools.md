+++
title = 'Ajax File Uploads With MooTools'
categories = ['javascript']
proof = false
+++
I've been working on redoing my personal site; making it responsive and easier for me to update (because even though I can edit the source, having a CMS is way easier). I thought it was going to be a pretty easy project (besides the design) because I knew what browser I was going to be using and don't have to include all these hacks for older browsers. But instead of just putting something really ugly together for the CMS admin, I've put a lot of time into making it look good. It's also become a place to test out some technologies. So far I created a modal plugin for MooTools (my preferred JS library) which was originally suppose to work on both mobile and desktop devices (but there were so many variables in the end it didn't work out). Tonight I just finished up a plugin for Ajax file uploads.

#### Ajax and File Uploads

Anyone who has tried to upload files via Ajax knows up until recently there was no true way of ajax file uploads. If you wanted to upload a file, you had to use the iFrame hack or use a Flash script to do it. But with new technologies, it has become possible to truly upload files with Ajax. The technology that's used is called FormData. This isn't available in all browsers, so you'll still have to have a fallback for older browsers. If you want to learn how to work with FormData, there's a really nice [tutorial](http://net.tutsplus.com/tutorials/javascript-ajax/uploading-files-with-ajax/) on Nettuts+ about ajax file uploading with jQuery. This tutorial didn't transfer over to MooTools, however. The MooTools Request class doesn't seem to support file uploads. I did a little researching around on the internet and found another MooTools plugin that allowed for Ajax file uploads, but there were a couple issues with it. First it required that a form tag was being used, second it only allowed for one upload input, third it turned that into a multi file upload input and added a lot of extra markup to the form. This didn't work for me at all. I wanted to be able to not have a form element, allow for multiple file inputs (but not a multi file input), and I didn't want extra markup inserted into my page. I took part of the source code (the part that actually did the file upload) and modified it.

{{< gist ivorisoutdoors 1342473 File.Upload.js >}}

(Full source with examples at [https://gist.github.com/1342473](https://gist.github.com/1342473).)

Again, this uses the FormData object, so this will not work with all browsers (I didn't include a fallback).

#### Usage

Including this plugin on your page (requires MooTools and the MooTools Request class), will give you access to two classes, File.Upload and Request.Upload. Request.Upload is used in the File.Upload class and usually won't be accessed directly by you (unless you really want to extend the plugin or want to make your own).

To create our Request object, we simply create a new File.Upload object.

{{< highlight javascript >}}
var upload = new File.Upload({
  // upload options
});
{{< /highlight >}}

##### Options

There are a couple of options available to us.

* url: where to send the request (defaults to current page)
* onComplete: the function to call when request is completed
* data: any extra data to send (not images) in an object
* images: an array of file input id's to upload

##### Methods

File.Upload offers a couple of methods to add both regular form data and images to the ajax request.

###### Add A Single Image

To attach an image, use the add method.

{{< highlight javascript >}}
upload.add('image');
{{< /highlight >}}

The single parameter in this method is the id of the file input you want to add. This should be called after an image is added and not before.

#### Add Multiple Images

To add multiple images, just pass an array of file input ids to the addMultple method.

{{< highlight javascript >}}
upload.addMultiple(['image', 'image2', 'anotherImage']);
{{< /highlight >}}

#### Add Data

To add data, pass an object to the data method.

{{< highlight javascript >}}
upload.data({
  foo: 'bar'
});
{{< /highlight >}}

Passing both image data and regular form data is complex. I found a simple workaround, pass regular data as GET variables rather then POST. This means you will have to use $_GET rather then $_POST (php) in your server side scripts.

#### Send The Request

Once you have attached all your data and are ready to send the request, use the send method.

{{< highlight javascript >}}
upload.send();
{{< /highlight >}}

As of right now (read as "this will probably be taken out later"), if you pass a parameter, it will add the image with the same id to the request before sending it.

#### Feedback

While this plugin was really built just to fit the needs of my site, I hope you can maybe find some use in it. If you find any bugs with it, or have any feedback, leave me a comment (either here or on the Gist itself). If you want to extend it, go for it. Fork the [Gist](https://gist.github.com/1342473) and make it better.

Happy file uploading!

*Update 1/20/12*: Since writing this, I have switched over my site to a Jekyll setup.
