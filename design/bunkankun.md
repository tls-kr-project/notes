# Bunkankun

## Design objectives for the Bunkankun project

There are three parts of the project:
- A vision for the shape of data for the texts and other digital objects accessable to the project
- A definition for middleware that acts as bridge between the data and the interfaces
- Clients that provide access to the texts for the users

All of these have their own additional parts and requirements. There are also additional aspects, such as the fact that deliverables of all three of these parts will be made available with a CC-BY-SA license, any contributions are expected to use the same license.

## Shape of the data: The BKK Bundle

We want to have a data format that can be validated and audited and fits well with the current distributed infrastructure for distribution of digital data. Its coherence should be verifiable and its changes traceable. 

We strive for a format that can hold the basic artifacts in a verifiable and modular manner. That can be easily shared. 

In addition to the **archival format**, there will be a **recipe format** that can be used by the clients to retrieve content and compose content in a variety of formats.  For example, in a discussion of Chapter two of the Lunyu, we could share a BKK recipe file that points to a version of the text, and various related assets deamed important by the author of the recipe.  A receiver could then ask for a rendering in a format suitable for the receiving environment, as PDF, DOC text or other formats.  Depending on the requirements of the user, additional assets (translations, annotations, dictionary entries) could be added in this step.

### Archival format

This is mainly for text data of premodern Chinese. Texts used typically to be transmitted in scrolls, *juan* in Chinese. This is a handy subdivision for long texts and still used for that purpose today.  In our format, we will have each juan in a separate file and a manifest plus a table of contents and some additional metadata pertaining to the whole text, they will point into the juan files.   All `text` fields, no matter the location or the type, will be accompanied by a `hash` field, the value of which will be used to audit the content.

A **juan** file will have a front, body and back; only the body has to be non-empty, the others are optional and need not be present if empty. Additional metadata fields will be available.  The text elements of the body and back might be divided if appropriate, typical examples for a front would be a opening line that locates the juan in a larger collection, the title of the text, sequential number of the juan and an attribution, containing name and role of persons with respect to the content of the body. The back will have a closing line.  The positioning of prefaces, postfaces, colophons and other paratextal features is open -- they might either go into the body or be separated out in front and back. 

The body will have one text element that contains all text character of the whole content of the juan.  Space characters, punctuation, line breaks and similar content will be moved to a **markers** object that immediately follows the text element. The type of markers is an open set, they will typically include all layout features, page and line breaks, indent characters, punctuation.  A marker will need to have a **type** and an **offset** field, other fields are optional, but typically will include **id**, **content** etc; the fields might contain structured content as well.  

The **manifest**, which is an entry point for the whole text, will list the included assets, possibly refers to other assets elsewhere and will have a **canonical identifier** and a **canonical location**. 

## Middleware

All requests to the texts will be routed through this middleware, a software library that provides access to the texts in an abstracted form that does not need to care about where exactly the texts are located.

## References

Here are some projects or documents that provided inspiration and could be used to guide further fleshing out of this design document. 

- Distributed text services  https://dtsapi.org/specifications/
- Text Encoding Initiative 
- IIIF
- Docker https://en.wikipedia.org/wiki/Docker_%28software%29

