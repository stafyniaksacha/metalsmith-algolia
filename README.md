- [Installation](#Installation)
- [Usage](#Usage)
  - [Javascript API](#Javascript-API)
  - [Templates metadata](#Templates-metadata)
  - [Indexed document](#Indexed-document)
- [Customize indexed documents](#Customize-indexed-documents)
- [Options reference](#Options-reference)
- [Todolist](#Todolist)

# metalsmith-algolia

> A metalsmith plugin for indexing content on Algolia

This plugin allows you to index your content in Algolia search engine on metalsmith building process.

_If this plugin doesn't fit your needs, please don't hesitate to ask for feature requests._

## Installation
```bash
npm install --save metalsmith-algolia
```

## Usage


#### Javascript API

The exemple bellow show the minimum code required to index your content. _(node: metalsmith-markdown is not required)_

```js
const metalsmith = require('metalsmith');
const markdown = require('metalsmith-markdown');
const algolia = require('metalsmith-algolia');

metalsmith(__dirname)
  .source('./src')
  .use(markdown())
  .use(algolia({
    projectId: '<algolia-project-id>',
    privateKey: '<algolia-private-key>',
    index: '<algolia-index>'
  }))
  .build();
```
> please, use command line arguments or environement variables to store your algolia private key

#### Templates metadata

Here is an exemple with a fake markdown template `./src/mypage.md`

```markdown
---
title: My awesome static page !
description: This is a exemple page
algolia: true
---
# My awesome static page !
content exemple
```

#### Indexed document

By default, metadata _(string/boolean/integers)_ and contents will be sent to Algolia for all files with `algolia: true` metadata  
With this exemple, the generated document will look like:

```json
{
  "title": "My awesome static page !",
  "description": "This is a exemple page",
  "contents": "<h1>My awesome static page !\n<p>content exemple</p>"
}
```


## Customize indexed documents

If you need to cleanup your contents, compute additional fields, or remove metadata from indexing, you can use the `fileParser` option to plugin constructor to give a custom callback to generate your own documents:


```js
const metalsmith = require('metalsmith');
const markdown = require('metalsmith-markdown');
const algolia = require('metalsmith-algolia');
const cheerio = require('cheerio');

function customFileParser(file, metadata) {
  let documents = [];
  let $ = cheerio.load(metadata.contents.toString());

  // add as many as documents as you need
  documents.push({
    title: metadata.title,
    contents: $('p').text();
  })

  return documents;
}

metalsmith(__dirname)
  .source('./src')
  .use(markdown())
  .use(algolia({
    projectId: '<algolia-project-id>',
    privateKey: '<algolia-private-key>',
    index: '<algolia-index>',
    fileParser: customFileParser
  }))
  .build();
```

This time, the generated document will look like:

```json
{
  "title": "My awesome static page !",
  "contents": "content exemple"
}
```

## Options reference
| name   |  default  |  description  |
| --- | --- | --- |
| `projectId` |  | *(required)* Algolia project identifier |
| `privateKey` |  | *(required)* Algolia private key |
| `index` |  | *(required)** Algolia index |
| `clearIndex` | false | Clear Algolia index before indexing new documents |
| `fileParser` | null | Function reference to a custom handler for building documents |

> *hint: metalsmith-algolia use `debug`*

## Options reference

## Todolist

- [ ] Add tests/linter
- [ ] Add travis configuration
- [ ] Try bulk operations (without fail fast)
