---
layout: post
identifier: url_unfurl
title: Slack Like URL Unfurling In Rails
description: How to achieve slack like url unfurling in rails, extracting relevant deta from meta tags, facebook open graph tags or twitter tags.
---

We working on a chat functionality in a real estate site between the brokers, dealers and customers, wherein they wanted to share the property videos, images and external links. We were impressed with the **slack link unfurling** so wanted to implement something similar for our users.

There are a few rubygems which can fetch the desired  **meta tags** from a url.

1. [open graph](https://github.com/intridea/opengraph) - Did not use this as it's not being maintained and lacked a few desired features
2. [link thumnailer](https://github.com/gottfrois/link_thumbnailer) - It gives basic information like image, video, title, description, and favicon. We wanted to do more. We wanted to check for the og:type so that we can show an image if it's of type image, embedd a video if it was a video link and so on.
3. [meta inspector](https://github.com/jaimeiniesta/metainspector) - **Finally!**, found the one as per our requirements. It gives all the basic open graph and site details along with the ability to see the entire meta tags tree and fetch the desired information.


Now the next thing was to save the time taken to fetch meta tag info. So we implemented caching on the html level to cache the entire html along with the image and video tags as we needed to show the chat to multiple users in a chat room or a one to one chat and ofcourse show the entire chat history. Therefore, caching the entire html was our way to go.

So we created a separate service in the app/services and used it as and when required. We cached the entire html. One can cache only the resultant meta data

```page = MetaInspector.new(@url)```

The service we wrote is as follows

```ruby

  class MetaExtractorService
    include ActionView::Helpers

    def initialize(url)
      @url = url
    end

    def html
      Rails.cache.fetch(@url) do
        html = ''
        begin
          page = MetaInspector.new(@url)
          html += page_details(page)
        rescue MetaInspector::ParserError => e
          html += video_or_image_details(e.message)
        rescue Exception => e
          Rails.logger.info(e.message)
        end
        html.html_safe
      end
    end

    private

    def page_details(page)
      html = "<div>"
      if page.images.favicon
        html += "<div>#{ image_tag(page.images.favicon, class: 'favicon') }</div>"
      end
      if page.meta_tags["property"]["og:site_name"].present?
        html += "<div class='site-name'> #{ page.meta_tags["property"]["og:site_name"].first }</div>"
      end
      html +=  "<div class='title'>#{ page.best_title }</div>"
      if page.meta_tags["property"]["og:type"].try(:first) == 'video'
        html += "<div><iframe src='#{ page.meta_tags["property"]["og:video:url"].try(:first) }' style='border: 0px;'></iframe></div>"
      else
        html += "<div class='description'>#{ page.description }</div></div>"
        html += "<div class='image'>#{  image_tag(page.images.best) }</div>"
      end
      html += "</div>"
      html.html_safe
    end

    def video_or_image_details(message)
      html = '<div>'
      if message =~ /image\//
        html += image_tag(@url)
      elsif message =~ /video\//
        html += "<iframe src='#{ @url }'></iframe>"
      end
      html += '</div>'
      html.html_safe
    end

  end

```