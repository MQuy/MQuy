## Character encoding

An encoding defines a mapping between bytes and text. You should always specify the encoding used for an HTML or XML page. If you don't, you risk the characters in your content are incorrectly interpreted.

For HTML, you can define the coding of your document using a `meta` tag.

```html
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <!-- or -->
    <meta http-equiv="Content-Type" content="text/html, charset=utf-8" />
  </head>
</html>
```

HTTP header has a higher precedence than in-document `meta` declarations. The best practice is we have to make sure that HTTP header `Content-Type` is the same as `meta charset`.

One of HTTP `Content-Type` header benefits is ISP or intermediate severs could take advantage of this information. For `meta`, it helps developers, tests to visually check the encoding of document or browsers to display a static document.

### References

- [Declaration character encoding in HTML](https://www.w3.org/International/questions/qa-html-encoding-declarations)
- [Character encoding](https://developer.mozilla.org/en-US/docs/Glossary/character_encoding)
