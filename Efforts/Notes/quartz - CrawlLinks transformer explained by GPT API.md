---
category: "[[ChatGPT]]"
up: 
related:
  - "[[ChatGPT]]"
  - "[[Quartz]]"
aliases:
---
This code is a module that exports a Quartz transformer plugin called "CrawlLinks". This plugin is responsible for transforming links found in Markdown files.

The plugin takes in an optional configuration object called `userOpts` that can override the default options. The default options are defined in the `defaultOptions` object.

The `CrawlLinks` function returns an object with a `name` property set to "LinkProcessing" and an `htmlPlugins` function that returns an array of HTML processing functions.

Each HTML processing function in the array takes in a `tree` of HAST (Hypertext Abstract Syntax Tree) nodes and a `file` object that contains additional data. Inside the processing function, the code traverses the HAST tree using the `unist-util-visit` library to find and manipulate specific elements.

The main logic of the plugin is inside the visit callback function. It looks for `<a>` tags and checks if they have an `href` property. If the `href` value is a string, it performs various transformations on the link.

If the link is an external URL, it adds the class "external" to the `<a>` tag and, if the `externalLinkIcon` option is enabled, appends an SVG icon as a child of the `<a>` tag.

If the link is an internal URL, it applies the transformation specified by the `markdownLinkResolution` option and sets the `href` property to the transformed link. It also adds the classes "internal" and "alias" if the link has an alias text different from the href value.

If the `openLinksInNewTab` option is enabled, it sets the `target` property of the `<a>` tag to "_blank".

If the `prettyLinks` option is enabled and the link is an internal URL with a text child node that is not a hash link, it modifies the text value to be the basename of the link.

The code also checks for other elements like `<img>`, `<video>`, `<audio>`, and `<iframe>` tags and performs a similar transformation on their `src` properties.

Finally, the code collects all the outgoing internal links found in the Markdown file and stores them in the `file.data.links` array.

The code also extends the `vfile` module to include a new `links` property in the `DataMap` interface, allowing other parts of the codebase to access the links data.
