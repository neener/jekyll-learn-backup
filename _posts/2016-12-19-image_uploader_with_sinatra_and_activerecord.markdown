---
layout: post
title:  "Image Uploader with Sinatra and ActiveRecord"
date:   2016-12-19 23:54:35 +0000
---


I wanted to try to build an image uploader in Sinatra. My initial instinct was to use a gem. The paperclip gem has a lot of stars and forks on Github so I read through the documentation. 

[Paperclip Gem](https://github.com/thoughtbot/paperclip)

I wasn’t sure if this gem could be used with Sinatra, all the documentation seemed to be referring to Rails procedures.

After digging around for a simpler way to handle the uploader I came across these blogs:

[Sinatra Managing File Uploads](http://azemoh.com/2016/05/17/sinatra-managing-file-uploads/)

[Uploading Images with Ruby and Sinatra](http://www.gideondsouza.com/blog/uploading-images-with-ruby-and-sinatra/)

I decide to take the advice and hand code the image uploader. Thanks to the above blogs I have a general outline of what my controller and views should look like. 

I broke down building the image uploader into 4 steps:

1 - In my upload model I set the Upload class to inherit from ActiveRecord::Base. Then I associate uploads with a category using the `belongs_to` method.

```
class Upload < ActiveRecord::Base
	belongs_to :category
end
```

2 - I create an uploads table in my ActiveRecord database. The `image_url` gets saved in the database as a string. I also include a `category_id` to associate the upload with the category it belongs to.

```
class CreateUploads < ActiveRecord::Migration[5.0]
  def change
  	create_table :uploads do |t|
  		t.string "title"
    	t.integer "category_id"
    	t.string :image_url
    	t.timestamps
  	end
  end
end
```

3 - I create two views, a form for the image uploader and a category.uploads iteration to show each image after its been uploaded. An important piece to add to the form for image uploads is the 
`enctype="multipart/form-data”`. This with instantiate your UploadsController class with a set of necessary params to grab the file upload. If I inspect my UploadsController I can see the hashes that are created from including enctype in my form. It looks like this:

```
[1] pry(#<UploadsController>)> params
=> {"category_id"=>"12",
 "title"=>"fdsa",
 "file"=>
  {:filename=>"bloodbud.jpg",
   :type=>"image/jpeg",
   :name=>"file",
   :tempfile=>
    #<File:/var/folders/mc/yn88kbcj71x4z7fd8py9g_cm0000gn/T/RackMultipart20161219-85185-n2viqo.jpg>,
   :head=>
    "Content-Disposition: form-data; name=\"file\"; filename=\"bloodbud.jpg\"\r\nContent-Type: image/jpeg\r\n"}}
```

The uploads form looks like this:

```
<h4>Upload Image</h4>
<form action="/uploads" method="POST" enctype="multipart/form-data">
	<input type="hidden" name="category_id" value="<%=@category.id%>" />
	Title: <input type="text" name="title"></br>
	<input type="file" name="file">
	<input type="submit" value="Upload">
</form>
```

I put both the upload form and the show_upload views in the show_category view in order to keep it all on one page. 

This is how I set up showing each new upload. I’m iterating over each category.upload and put each uploads title and image_url to the page:


```
<% @category.uploads.each do |category_upload| %>
	<ul>
		<li>
			<%= category_upload.title %></br>
			<img src="/uploads/<%=category_upload.image_url %>" />

			<form action="/uploads/<%=category_upload.id%>/delete" method="POST">
				<input id="hidden" type="hidden" name="_method" value="delete">
				<button type="submit"> Delete </button>
			</form>
		</li>
	</ul>
<% end %>
```

4 - The upload_controller has a post route that contains the code for checking to see if a user uploaded a file and then writes the file to disk. In the post route, first i grab the category instance by its id. I set file to grab the params from the :tempfile, through the :file hash. I set the filename instance to then grab the :filename, thought the :file hash. Then instantiate a new Upload and set the upload.image_url, upload.category, and upload.title to their respective paths. Then I call save on upload. File.open is the piece of code that reads the file from your desktop and writes it to disk. I then redirect to the category url with the id of the category I was uploading images to. 

```
post '/uploads' do
		
		@category = Category.find_by_id(params[:category_id])
		@filename = params[:file][:filename]
  		file = params[:file][:tempfile]
  		@upload = Upload.new
  		@upload.image_url = @filename
  		@upload.category = @category 
  		@upload.title = params[:title]
  		@upload.save
  		
  		File.open("./public/uploads/#{@filename}", 'wb') do |f|
    		f.write(file.read)
  		end

		redirect "/categories/#{@category.id}"
	end

```






