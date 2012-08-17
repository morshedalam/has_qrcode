# HasQrcode

This gem provides qrcode support to your active_record models. It allows people to generate qrcode images and store them on filesystem or s3. It uses `mini_magick` gem as its dependency. This means that you must install ImageMagick on your system.

## Installation

Add this line to your application's Gemfile:

    gem 'has_qrcode'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install has_qrcode

## Quick Start

In your model:

    class Article < ActiveRecord::Base
      has_qrcode   :data    => :to_vcard,      # required
                   :logo    => :logo_url,      # optional
                   :size    => "250x250",      # optional, default is "250x250"
                   :margin  => "5",            # optional, default is "0"
                   :format  => ["png", "eps"], # optional, default is "png"
                   :ecc     => "L",            # optional, default is "L"
                   :bgcolor => "fff",          # optional, default is "fff"
                   :color   => "000",          # optional, default is "000"
                   :processor => :qr_server    # optional, default is :qr_server for now (might be changed in the future)
                   :storage => { :filesystem => { :path => ":rails_root/public/system/:table_name/:id.:format" } }
    end

In your migrations:

    class AddQrCodeToArticle < ActiveRecord::Migration
      def change
        add_column :articles, :qrcode_filename, :string
      end
    end

In your show view:

    <%= image_tag @article.qrcode_url(:png) %>

HasQrcode Options

By default, all has_qrcode options are evaluated at runtime on instance object level so that it can be dynamically for each instance. You can specify the value as symbol or a proc object.

Processor

Currently, HasQrcode supports only one processor which connects to the QR-Server API, http://qrserver.com/api/documentation/.

    has_attached_file :processor => :qr_server

Storage

HasQrcode ships with 2 storage adapters:

    File Storage
    S3 Storage (via aws-sdk)
    
The image files that are generated, by default, placed in the directory specified by the :storage option to has_qrcode. By default, on :filesystem the location is :rails_root/public/system/:table_name/:id/:filename.:format.

    has_qrcode  :storage => { :filesystem => { :path => ":rails_root/public/system/:table_name/:id.:format" } }

You may also choose to store your files using Amazon's S3 service. To do so, include the aws-sdk gem in your Gemfile:

    gem 'aws-sdk', '~> 1.3.4'

And then you can specify using S3 from has_qrcode.

    has_qrcode  :storage => { :s3 => { :bucket => "qrcode-images", :prefix => "kh", :acl => :public_read, :cache_control => "max-age=28800" } }
    
By default, the qrcode_filename is generated randomly using the standard ruby library `SecureRandom`.

Rake Script

This gem provides one rake script to generate qrcode images for a specified model.

    $ rake qrcode:generate[model_name,scope_name,scope_value]
    $ rake qrcode:generate[Article,by_author,Chamnap] # generate qrcode images for Article posted by author, Chamnap.
    
TODO
- Add more specs
- Support rqrcode processor and google-qr
- Support multiple sizes
- Refactor code
