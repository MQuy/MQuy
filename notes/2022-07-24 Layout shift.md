## Layout shift

By default, image takes zero space until browser load and parse that image to know its dimension. We could use aspect-ratio or width/height hints to prevent layout shift.

### Image or Background Image

It comes down to the question: is it a part of the content (`<img />`) or the design (`background-image`)?

- content: SEO, accessiblity, performance (using better supported format via `<picture />`, lazy loading/encoding).
- design: transform, animation ...

### References

- [Avoiding <img> layout shifts: aspect-ratio vs width & height attributes](https://jakearchibald.com/2022/img-aspect-ratio/)
